# -
用C++和easyx实现 数字华容道以及拼图华容道

代码块:
/*
不同主体要进行的更改有:
col
row
WIDTH
HEIGHT
bkindex
image的图片内容
背景空白图片的内容
*/
#include<iostream>
#include <conio.h>
#include <ctime>
#include<graphics.h>
#include<easyx.h>
#include<random>
#include<Windows.h>
#include<string>
#include<stdio.h>
using std::vector;
using std::endl;

//全局变量
int col = 4;
int row = 3;
int WIDTH = 1000;
int HEIGHT = 666;
int bkindex = 11;
int bkrow, bkcol;
IMAGE image, bk;
int mode=-1;
vector<vector<int>> mass;
vector<vector<int>> pos;
IMAGE i;
IMAGE images[8];
int textWidth, textHeight, x, y;

//函数声明区域
void Loadimage();
void showimage();
void gameover();
//根据索引 判断x,y坐标
int fx(int index) { return (index % col) * (WIDTH / col); }
int fy(int index) { return (index / col) * (HEIGHT / row); }
//判断随机关卡是否有解的函数
bool isSolvable(const vector<int>& puzzle) {
	int inversions = 0;
	int row_num = 0;
	for (int i = 0; i < puzzle.size() - 1; ++i) {
		for (int j = i + 1; j < puzzle.size(); ++j) {
			if (puzzle[i] == bkindex) {
				row_num = row - 1 - i / col;
				break;
			}
			else if (puzzle[i] > puzzle[j]) {
				++inversions;
			}
		}
	}
	//逆序数的奇偶性和倒数行数(最后一行记作倒数第0行)的奇偶性一致
	return inversions % 2 ==0&& row_num % 2==0;
}





int main() {
	//初始界面UI
	initgraph(600,600);
	setbkcolor(WHITE);
	setbkcolor(RGB(200,190,231));
	//setbkcolor(RGB(195,195,195));
	cleardevice();
	setfillcolor(BLACK);
	fillrectangle(0,0,600,200);
	settextcolor(WHITE);
	settextstyle(60, 0, _T("Arial")); // 设置文字样式
	setbkcolor(BLACK);
	outtextxy(75,80,_T("Sliding-Block  Game") );
	    //下方文字设置
	settextcolor(RGB(237, 214, 109));
	settextstyle(30, 0, _T("微软雅黑")); 
	//setbkcolor(BLACK);
	//setbkcolor(RGB(255,174,201));//粉色
	setbkcolor(RGB(195,195,195));//灰色
	//模式1
	settextcolor(RGB(255, 255, 0));
	textWidth = textwidth(_T("4×4  湖人队配色经典版")); // 计算文字的宽度
	x = (600 - textWidth) / 2; // 计算文字的横坐标，使其居中
	outtextxy(x,270, _T("4×4  湖人队配色经典版"));
	//模式2
	settextcolor(RGB(237, 28, 36));
	textWidth = textwidth(_T("3×3  红色玫瑰花拼图")); // 计算文字的宽度
	x = (600 - textWidth) / 2; // 计算文字的横坐标，使其居中
	outtextxy(x, 340, _T("3×3  红色玫瑰花拼图"));
	//模式3
	settextcolor(RGB(94, 255, 255));
	textWidth = textwidth(_T("4×4  卡塔尔世界杯捧杯拼图")); // 计算文字的宽度
	x = (600 - textWidth) / 2; // 计算文字的横坐标，使其居中
	outtextxy(x, 410, _T("4×4  卡塔尔世界杯捧杯拼图"));
	//模式4
	settextcolor(RGB(72, 82, 232));
	textWidth = textwidth(_T("3×4  泉城广场夜景拼图")); // 计算文字的宽度
	x = (600 - textWidth) / 2; // 计算文字的横坐标，使其居中
	outtextxy(x, 480, _T("3×4  泉城广场夜景拼图"));
	//随机模式
	settextcolor(WHITE);
	textWidth = textwidth(_T("选择困难  随便一个就行")); // 计算文字的宽度
	x = (600 - textWidth) / 2; // 计算文字的横坐标，使其居中
	outtextxy(x, 550, _T("选择困难  随便一个就行"));
	IMAGE sima1, sima2;
	loadimage(&sima1,_T("bupt.png"));
	loadimage(&sima2, _T("scs.jpg"));
	//putimage(0,450,&sima1);
	//putimage(500,500,&sima2);
	
	while (mode == -1) {
		MOUSEMSG msg1 = GetMouseMsg();
		if (msg1.uMsg == WM_LBUTTONDOWN) {
			int x = msg1.x, y = msg1.y;
			//根据坐标 确定模式的改变值
			if (x>(600 - textwidth(_T("4×4  湖人队配色经典版"))) /2
				&&x< (600 - textwidth(_T("4×4  湖人队配色经典版"))) / 2+ textwidth(_T("4×4  湖人队配色经典版"))
				&&y>270&&y<300
				) {
				mode = 1;
			}
			else if (x > (600 - textwidth(_T("3×3  红色玫瑰花拼图"))) / 2
				&& x < (600 - textwidth(_T("3×3  红色玫瑰花拼图"))) / 2 + textwidth(_T("3×3  红色玫瑰花拼图"))
				&& y>340 && y < 370) {
				mode = 2;
			}
			else if (x > (600 - textwidth(_T("4×4  卡塔尔世界杯捧杯拼图"))) / 2
				&& x < (600 - textwidth(_T("4×4  卡塔尔世界杯捧杯拼图"))) / 2 + textwidth(_T("4×4  卡塔尔世界杯捧杯拼图"))
				&& y>410 && y < 440) {
				mode = 3;
			}
			else if (x > (600 - textwidth(_T("3×4  泉城广场夜景拼图") ) ) / 2
				&& x < (600 - textwidth(_T("3×4  泉城广场夜景拼图"))) / 2 + textwidth(_T("3×4  泉城广场夜景拼图"))
				&& y>480 && y < 510) {
				mode = 4;
			}
			else if (x > (600 - textwidth(_T("选择困难  随便一个就行"))) / 2
				&& x < (600 - textwidth(_T("选择困难  随便一个就行"))) / 2 + textwidth(_T("选择困难  随便一个就行"))
				&& y>550 && y < 580) {
				std::random_device rd;
				std::mt19937 gen(rd());
				std::uniform_int_distribution<int> p(1,4);
				mode = p(gen);
			}
		}
		//设置col row
		//WIDTH  HEIGHT
		//bkindex
		//image的图片内容 背景空白图片的内容
		//pos数组 mass数组
		switch (mode){
		//Lakers 经典
		case 1:
			col = 4;
			row = 4;
			WIDTH = 400;
			HEIGHT = 400;
			bkindex = 15;
			mass = {
				{2 , 3,  1 ,4},
				{6 ,5 , 8 , 7},
				{9 ,10 ,12 ,11},
				{14 ,15 ,13, 0}
			};
			pos = {
				{0,1,2,3},
				{4,5,6,7},
				{8,9,10,11},
				{12,13,14,15}
			};
			break;
		//玫瑰花
		case 2:
			col = 3;
			row = 3;
			WIDTH = 500;
			HEIGHT = 500;
			bkindex = 8;
			mass ={
				{1,2,5},
				{0,6,4},
				{7,8,3}
			};
			pos ={
				{0,1,2},
				{3,4,5},
				{6,7,8}
			};
			break;
		//阿根廷捧杯
		case 3:
			col = 4;
			row = 4;
			WIDTH = 1000;
			HEIGHT = 692;
			bkindex = 15;
			mass ={
				{2 , 3,  1 , 4},
				{6 ,5 , 8 , 7},
				{9 ,10 ,12 ,11},
				{14 ,15 ,13, 0}
			};
			pos ={
				{0,1,2,3},
				{4,5,6,7},
				{8,9,10,11},
				{12,13,14,15}
			};
			break;
		//济南夜景
		case 4:
			col = 4;
			row = 3;
			WIDTH = 1000;
			HEIGHT = 666;
			bkindex = 11;
			mass ={
				{2 , 3,  1 , 4},
				{6  ,5 , 8 , 7},
				{9  ,10 ,0 ,11}
			};
			pos ={
				{0,1,2,3},
				{4,5,6,7},
				{8,9,10,11}
			};
			break;
		default:
			break;
		}
		//关闭窗口
		if(mode!=-1) closegraph();
	}


	//初始化 显示图片 打乱
	initgraph(WIDTH, HEIGHT);

	switch(mode){
	case 1:
		setbkcolor(RGB(85, 37, 130));
		cleardevice();
		loadimage(&i, _T("start.jpg"));
		putimage(0, 220, &i);
		settextcolor(RGB(255, 255, 0));
		settextstyle(50, 0, _T("微软雅黑"));
		outtextxy(85, 50, _T("Lakers' 4×4"));
		outtextxy(60, 120, _T("Fifteen Puzzle"));
		setlinecolor(WHITE);
		rectangle(57, 47, 325, 175);
		break;
	case 2:
		setbkcolor(WHITE);
		cleardevice();
		settextcolor(RGB(255, 192, 203)); // 设置粉色字体颜色
		settextstyle(40, 0, _T("Arial")); // 设置文字样式
		textWidth = textwidth(_T("You have 10s to remember")); // 计算文字的宽度
		textHeight = textheight(_T("You have 10s to remember")); // 计算文字的高度
		x = (WIDTH - textWidth) / 2; // 计算文字的横坐标，使其居中
		y = (HEIGHT - textHeight) / 2; // 计算文字的纵坐标，使其居中
		outtextxy(x, y-50, _T("You have 10s to remember"));
		break;
	case 3:
		setbkcolor(RGB(135, 206, 235));
		cleardevice();
		settextcolor(WHITE); // 设置粉色字体颜色
		settextstyle(70, 0, _T("Arial")); // 设置文字样式
		textWidth = textwidth(_T("You have 10s to remember")); // 计算文字的宽度
		textHeight = textheight(_T("You have 10s to remember")); // 计算文字的高度
		x = (WIDTH - textWidth) / 2; // 计算文字的横坐标，使其居中
		y = (HEIGHT - textHeight) / 2; // 计算文字的纵坐标，使其居中
		outtextxy(x, y - 100, _T("You have 10s to remember"));
		break;
	case 4:
		setbkcolor(RGB(255, 170, 38));
		cleardevice();
		settextcolor(WHITE); // 设置粉色字体颜色
		settextstyle(70, 0, _T("Arial")); // 设置文字样式
		textWidth = textwidth(_T("You have 10s to remember")); // 计算文字的宽度
		textHeight = textheight(_T("You have 10s to remember")); // 计算文字的高度
		x = (WIDTH - textWidth) / 2; // 计算文字的横坐标，使其居中
		y = (HEIGHT - textHeight) / 2; // 计算文字的纵坐标，使其居中
		outtextxy(x, y - 50, _T("You have 10s to remember"));
		break;
	default:
		std::cout << "Mode is " << mode << endl;
		std::cout << "During UI" << endl;
		closegraph();
		exit(0);
		break;
	}
	// 随机打乱数字华容道索引，直到找到一个有解的排列
	vector<int> puzzle(row * col);
	for (int i = 0; i < puzzle.size(); ++i) {
		puzzle[i] = i;
	}
	srand(time(0));
	while (true) {
		//打乱顺序
		random_shuffle(puzzle.begin(), puzzle.end());
		if (isSolvable(puzzle)) {
			int index = 0;
			for (int i = 0; i < row; i++) {
				for (int j = 0; j < col; j++) {
					mass[i][j] = puzzle[index];
					index++;
				}
			}
			break;
		}
	}
	Sleep(500);
	//按下回车进入
	switch (mode){
	case 1:
		settextcolor(WHITE);
		settextstyle(30, 0, _T("Consolas"));
		outtextxy(60, 210, _T("Press enter to start!"));
		break;
	case 2:
		settextcolor(RGB(255, 142, 32));
		setbkcolor(RGB(255, 192, 203));
		settextstyle(30, 0, _T("Consolas")); 
		outtextxy(100, 300, _T("Press enter to start!"));
		break;
	case 3:
		setbkcolor(WHITE);
		settextcolor(RGB(81, 24, 181));
		settextstyle(40, 0, _T("Consolas")); // 设置文字样式
		outtextxy(300, 400, _T("Press enter to start!"));
		break;
	case 4:
		settextcolor(RGB(255, 142, 32));
		setbkcolor(YELLOW);
		settextstyle(50, 0, _T("Consolas")); // 设置文字样式
		textWidth = textwidth(_T("Press enter to start!")); // 计算文字的宽度
		x = (WIDTH - textWidth) / 2; // 计算文字的横坐标，使其居中
		outtextxy(x, 500, _T("Press enter to start!"));
		break;
	default:
		std::cout << "Mode is " << mode << endl;
		std::cout << "During return to start" << endl;
		closegraph();
		exit(0);
		break;
	}
	while (1) {
		if (GetAsyncKeyState(VK_RETURN)) {
			break;
		}
	}
	EndBatchDraw();
	cleardevice();
	//图片初始加载 经典华容道不需要这一步
	if (mode!=1){
		Loadimage();
		Sleep(10000);
	}
	else {
		//初始化空白格子位置 and 背景 
		setbkcolor(RGB(255, 255, 0));
		cleardevice();
	}

	//寻找bk块的位置
	bool tag = false;
	for (int i = 0; i < row; i++) {
		for (int j = 0; j < col; j++) {
			if (mass[i][j] == bkindex) {
				bkrow = i;
				bkcol = j;
				tag = true;
				break;
			}
		}
		if (tag == true) break;
	}
	showimage();
	cleardevice();
	showimage();
	//复原
	bool isover = false;
	while (!isover) {
		//鼠标点击  确定是否移动和交换位置
		MOUSEMSG msg = GetMouseMsg();
		if (msg.uMsg == WM_LBUTTONDOWN) {
			int x = msg.x, y = msg.y;
			int coltemp = x / (WIDTH / col);
			int rowtemp = y / (HEIGHT / row);
			int temppos = coltemp + rowtemp * col;
			//判断是否可以移动
			//位于同一行
			if (rowtemp == bkrow && coltemp > bkcol) {
				for (int i = bkcol; i < coltemp; i++) {
					mass[rowtemp][i] = mass[rowtemp][i+1];
				};
				mass[rowtemp][coltemp] = bkindex;
				bkcol = coltemp;
				cleardevice();
			}
			else if (rowtemp == bkrow && coltemp < bkcol) {
				for (int i = bkcol; i > coltemp; i--) {
					mass[rowtemp][i] = mass[rowtemp][i-1];
				};
				mass[rowtemp][coltemp] = bkindex;
				bkcol = coltemp;
				cleardevice();
			}
			//位于同一列
			else if (coltemp == bkcol && rowtemp > bkrow) {
				for (int i = bkrow; i < rowtemp; i++) {
					mass[i][coltemp] = mass[i + 1][coltemp];
				}
				mass[rowtemp][coltemp] = bkindex;
				bkrow = rowtemp;
				cleardevice();
			}
			else if (coltemp == bkcol && rowtemp < bkrow) {
				for (int i = bkrow; i > rowtemp; i--) {
					mass[i][coltemp] = mass[i-1][coltemp];
				}
				mass[rowtemp][coltemp] = bkindex;
				bkrow = rowtemp;
				cleardevice();
			}
		}
		//显示图片
		BeginBatchDraw();
		showimage();
		EndBatchDraw();
		//判断是否结束
		if (mass == pos) {
			Sleep(700);
			isover = true;
		}
	}
	//结束的一个界面
	EndBatchDraw();
	cleardevice();
	gameover();
	closegraph();
	return 0;
}

void Loadimage(){
	//初始化以及显示正常图片
	switch (mode){
	case 2:
		loadimage(&image, _T("red_flower.jpg"));
		loadimage(&bk, _T("bkpink.jpg"));
		break;
	case 3:
		loadimage(&image, _T("A.jpg"));
		loadimage(&bk, _T("background.png"));
		break;
	case 4:
		loadimage(&image, _T("city.jpg"));
		loadimage(&bk, _T("bkorg.jpg"));
		break;
	default:
		std::cout << "Mode is " << mode << endl;
		std::cout << "During loadimage()" << endl;
		closegraph();
		exit(0);
		break;
	}
	for (int i = 0; i < row; i++) {
		for (int j = 0; j < col; j++) {
			if (pos[i][j] == bkindex)  putimage(j * WIDTH / col, i * HEIGHT / row, WIDTH / col, HEIGHT / row, &bk, j * WIDTH / col, i * HEIGHT / row);
			else putimage(j * WIDTH / col, i * HEIGHT / row, WIDTH / col, HEIGHT / row, &image, j * WIDTH / col, i * HEIGHT / row);
		}
	}
	switch (mode) {
	case 2:
		setlinecolor(RED);
		break;
	case 3:
		setlinecolor(RED);
		break;
	case 4:
		setlinecolor(BLUE);
		break;
	}
	setlinestyle(PS_SOLID, 2);
	for (int i = 1; i < col; i++) line(i * WIDTH / col, 0, i * WIDTH / col, HEIGHT);
	for (int i = 1; i < row; i++) line(0, i * HEIGHT / row, WIDTH, i * HEIGHT / row);
}

void showimage() {
	//贴图的
	if (mode != 1) {
		for (int i = 0; i < row; i++) {
			for (int j = 0; j < col; j++) {
				//根据pos确定 贴的位置; 根据mass确定贴的内容
				if (mass[i][j] == bkindex)  putimage(j * WIDTH / col, i * HEIGHT / row, WIDTH / col, HEIGHT / row, &bk, fx(mass[i][j]), fy(mass[i][j]));
				else   putimage(j * WIDTH / col, i * HEIGHT / row, WIDTH / col, HEIGHT / row, &image, fx(mass[i][j]), fy(mass[i][j]));
			}
		}
	}
	//数字的
	else if(mode==1){
		BeginBatchDraw();
		TCHAR s[5];
		for(int i = 0; i < row; i++) {
			for (int j = 0; j < col; j++) {
				//pos确定 贴的位置; mass确定贴的内容
				if (mass[i][j] != bkindex){
					//数字转换为字符串
					settextcolor(RGB(85, 37, 130));
					settextstyle(40, 0, _T("微软雅黑"));
					swprintf_s(s, _T("%d"), mass[i][j] + 1);
					outtextxy(j * WIDTH / col + WIDTH / col / 2, i * HEIGHT / row + HEIGHT / row / 2, s);
				}
			}
		}
	}
	switch (mode) {
	case 1:
		setlinecolor(BLACK);
		break;
	case 2:
		setlinecolor(RED);
		break;
	case 3:
		setlinecolor(RED);
		break;
	case 4:
		setlinecolor(BLUE);
		break;
	}
	setlinestyle(PS_SOLID, 2);
	for (int i = 1; i < col; i++) line(i * WIDTH / col, 0, i * WIDTH / col, HEIGHT);
	for (int i = 1; i < row; i++) line(0, i * HEIGHT / row, WIDTH, i * HEIGHT / row);
	EndBatchDraw();
}

void gameover() {
	IMAGE i1;
	switch (mode)
	{
	case 1:
		// 背景
		setbkcolor(RGB(85, 37, 130));
		cleardevice();
		// 成功的文字
		settextcolor(RGB(255, 255, 0)); 
		setbkcolor(RGB(85, 37, 130)); 
		setbkmode(TRANSPARENT);
		settextstyle(28, 0, _T("Consolas")); 
		outtextxy(20, 300, _T("You are the King of the game!")); 
		//在左下角放图
		loadimage(&i1, _T("lakers.jpg"));
		putimage(0, 0, &i1);
		//离开提示
		Sleep(1500);
		settextcolor(RED);
		settextstyle(30, 0, _T("Consolas")); // 设置文字样式
		outtextxy(60, 350, _T("Press enter to leave"));
		break;
	case 2:
		// 绘制背景
		setbkcolor(RGB(255, 192, 203));
		cleardevice();
		// 绘制游戏成功的文字
		settextcolor(WHITE); 
		setbkcolor(RGB(255, 192, 203)); // 设置文字背景颜色与背景相同
		setbkmode(TRANSPARENT);
		settextstyle(30, 0, _T("Consolas")); 
		outtextxy(110, 80, _T("The rose is in bloom!"));
		outtextxy(170, 120, _T("So are you!")); 

		//在左下角放置图片
		loadimage(&i1, _T("flowers.jpg"));
		putimage(0, 170, &i1);

		//离开提示
		Sleep(1500);
		settextcolor(RED);
		setlinecolor(WHITE);
		rectangle(100, 10, 380, 40);
		outtextxy(100, 10, _T("Press enter to leave"));
		break;
	case 3:
		// 绘制背景
		setbkcolor(RGB(135, 206, 235));
		cleardevice();
		// 绘制游戏成功的文字
		settextcolor(WHITE); // 设置文字颜色为白色
		setbkcolor(RGB(135, 206, 235));
		setbkmode(TRANSPARENT);
		settextstyle(50, 0, _T("Consolas")); // 设置文字样式
		outtextxy(320, 400, _T("Congratulations")); // 在指定位置输出文字
		outtextxy(400, 500, _T("You Win")); // 在指定位置输出文字
		setlinecolor(WHITE);
		setlinestyle(PS_SOLID, 1);
		ellipse(240, 370, 730, 570);

		//在左下角放置图片
		loadimage(&i1, _T("cup.jpg"));
		putimage(250, 30, &i1);
		//加两个矩形
		setfillcolor(WHITE);
		fillrectangle(0, 0, 200, HEIGHT);
		fillrectangle(800, 0, WIDTH, HEIGHT);

		//离开提示
		Sleep(1500);
		settextcolor(BLACK);
		setlinecolor(WHITE);
		outtextxy(270, 600, _T("Press enter to leave"));
		break;
	case 4:
		setbkcolor(RGB(255, 170, 38));
		cleardevice();
		// 绘制游戏成功的文字
		settextcolor(WHITE); // 文字颜色
		setbkcolor(RGB(255, 170, 38));
		setbkmode(TRANSPARENT);
		settextstyle(50, 0, _T("Consolas")); // 文字样式
		textWidth = textwidth(_T("Congratulations! You win!")); // 计算文字的宽度
		textHeight = textheight(_T("Congratulations! You win!")); // 计算文字的高度
		x = (WIDTH - textWidth) / 2; // 计算文字的横坐标，使其居中
		y = (HEIGHT - textHeight) / 2; // 计算文字的纵坐标，使其居中
		outtextxy(x, y, _T("Congratulations! You win!")); // 在指定位置输出文字

		//放置图片

		loadimage(&images[0], _T("chao.jpg"));	loadimage(&images[1], _T("squ.jpg"));
		loadimage(&images[2], _T("ge.jpg")); 	loadimage(&images[3], _T("ssy.jpg"));
		loadimage(&images[4], _T("sdu.jpg")); 	loadimage(&images[5], _T("le.jpg"));
		putimage(0 + 100, 0, &images[0]); putimage(250 + 100, 0, &images[1]); putimage(500 + 100, 0, &images[2]);
		putimage(30, 476, &images[3]); putimage(280, 476, &images[4]); putimage(720, 476, &images[5]);

		//离开提示
		Sleep(1500);
		settextcolor(BLACK);
		setlinecolor(WHITE);
		//rectangle(100, 10, 380, 40);
		outtextxy(270, 400, _T("Press enter to leave"));
		break;
	default:
		break;
	}

	while (1) {
		if (GetAsyncKeyState(VK_RETURN)) {
			closegraph();
			std::cout << "太强了!  请收下我的膝盖!"<<std::endl;
			exit(0);
		}
	}
}

/*
介绍与总结:
(1) 本程序截至2023.08.13日,共有4个主题:
分别是: 洛杉矶湖人队配色的经典华容道   红色玫瑰花拼图   卡塔尔世界杯冠军碰杯拼图   山东省济南市泉城广场夜景拼图
同时 又添加了一个给选择困难症患者的福利  随机模式
(2) 经典华容道是直接开始,拼图华容道是先给10s记忆时间,然后万家根据感觉和记忆开始拼图
(3)保证打乱的拼图有解 (通过逆序数和空白行数的奇偶性一致实现)
(4)思路确实不难 UI能力有待提高 不喜勿喷

Coder: BUPT Liu Y.F.
*/

