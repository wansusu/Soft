# Soft
ruanjiangongchengshangji
#include<stdio.h>
#include<stdlib.h>
#include<time.h>
#include<Windows.h>
#include<malloc.h>
#include<math.h>
#define MAX_POINTS_NUMS 1000000
#define TIMES 10
#define ALPHA 0.2
#define FIRELOWER 0.9
#define CIRCLE_NUMS 8

struct circle {
	double x;
	double y;
	double radius;
};
struct circle Circles_Results[CIRCLE_NUMS];

double Culculate_D(struct circle Result[], int puted, double nowR,double tmpX,double tmpY) {
	int i, j, k;
	double minResult, tmpDTop, tmpDButtom, tmpDLeft, tmpDRight,tmpD;
	for (k = 0; k < puted; k++) {
		minResult = pow(Circles_Results[k].x - tmpX, 2) + pow(Circles_Results[k].y - tmpY, 2);
		if (nowR + Result[k].radius < pow(minResult, 0.5))
			continue;
		else
			tmpD += pow(Result[k].radius + nowR - pow(minResult, 0.5), 2);
	}
	tmpDTop = (tmpY + nowR > 1) ? nowR - (1 - tmpY) : 0;
	tmpDButtom = (tmpY - nowR < -1) ? nowR - (tmpY + 1) : 0;
	tmpDLeft = (tmpX - nowR < -1) ? nowR - (tmpX + 1) : 0;
	tmpDRight = (tmpX + nowR > 1) ? nowR - (1 - tmpX) : 0;

	tmpD += pow(tmpDTop, 2) + pow(tmpDButtom, 2) + pow(tmpDLeft, 2) + pow(tmpDRight, 2);
	tmpD /= puted;
	return tmpD;
}
void D2_PUTCIRCLES(struct circle Result[], int puted, double nowR) {
	int i, j, k, flag = 0;
	double tmpX = 0, tmpY = 0, tmpD = 0, tmpDLeft, tmpDRight, tmpDTop, tmpDButtom;
	double tmpx = 0, tmpy = 0, minResult;
	if (puted == 1) {
		Result[puted - 1].x = 0.0;
		Result[puted - 1].y = 0.0;
		Result[puted - 1].radius = 1.0;
		//puted++;
	}
	else if (puted >= CIRCLE_NUMS)
		return;

	srand((unsigned int)time(NULL));
	for (i = 0; i < MAX_POINTS_NUMS; i++) {
		flag = 0;
		tmpD = 0;
		tmpX = 2.0*(double)rand() / RAND_MAX - 1;
		tmpY = 2.0*(double)rand() / RAND_MAX - 1;
		tmpx = tmpy = 0;
		for (j = 0; j < TIMES; j++) {
			for (k = 0; k < puted; k++) {//Dx && Dy
				minResult = pow(Circles_Results[k].x - tmpX, 2) + pow(Circles_Results[k].y - tmpY, 2);
				if (nowR + Circles_Results[k].radius < pow(minResult, 0.5)) {
					tmpx += 0;
					tmpy += 0;
				}
				else {
					tmpx += 2 * ((Circles_Results[k].radius + nowR) - pow(minResult, 0.5))*(Circles_Results[k].x - tmpX) / pow(minResult, 0.5);
					tmpy += 2 * ((Circles_Results[k].radius + nowR) - pow(minResult, 0.5))*(Circles_Results[k].y - tmpY) / pow(minResult, 0.5);
				}
			}
			if (fabs(tmpx*ALPHA) <= 0.000001 && fabs(tmpy*ALPHA) <= 0.000001) {
				tmpX -= tmpx*ALPHA;
				tmpY -= tmpy*ALPHA;
				tmpx = tmpy = 0;
				flag = 1;
				break;
			}
			else {
				tmpX -= tmpx*ALPHA;
				tmpY -= tmpy*ALPHA;
				tmpx = tmpy = 0;
			}
		}
		//tmpD = Culculate_D(Result, puted, nowR, tmpX, tmpY);
		for (k = 0; k < puted; k++) {
			minResult = pow(Circles_Results[k].x - tmpX, 2) + pow(Circles_Results[k].y - tmpY, 2);
			if (nowR + Result[k].radius < pow(minResult, 0.5))
				continue;
			else
				tmpD += pow(Result[k].radius + nowR - pow(minResult, 0.5), 2);
		}
		tmpDTop = (tmpY + nowR > 1) ? nowR - (1 - tmpY) : 0;
		tmpDButtom = (tmpY - nowR < -1) ? nowR - (tmpY + 1) : 0;
		tmpDLeft = (tmpX - nowR < -1) ? nowR - (tmpX + 1) : 0;
		tmpDRight = (tmpX + nowR > 1) ? nowR - (1 - tmpX) : 0;

		tmpD += pow(tmpDTop, 2) + pow(tmpDButtom, 2) + pow(tmpDLeft, 2) + pow(tmpDRight, 2);
		tmpD /= puted;
		if (flag&&tmpD<=0.0001) {
			printf("ok\n");
			Result[puted].x = tmpX;
			Result[puted].y = tmpY;
			Result[puted].radius = nowR;
			//puted++;
			D2_PUTCIRCLES(Result, puted + 1, nowR)
			break;
		}
		else
			continue;
		
	}
	if (!flag&&nowR>0.01) {
		printf("!flag");
		D2_PUTCIRCLES(Result, puted, nowR*FIRELOWER);
	}
	return 0;
}

int main(void) {
	int i, j, k;
	D2_PUTCIRCLES(Circles_Results, 1, 0.5000);
	for (i = 0; i < CIRCLE_NUMS; i++)
		printf("x,%lf,y,%lf,r,%lf\n", Circles_Results[i].x, Circles_Results[i].y, Circles_Results[i].radius);
}
