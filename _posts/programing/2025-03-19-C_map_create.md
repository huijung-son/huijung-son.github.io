---
title: Map Create
author: cotes
date: 2025-03-19 10:00:00
categories: [Blogging, Programing]
tags: [C]
---

```c
#include <stdio.h>
#include <windows.h>

#define X 10
#define Y X
#define START 7

typedef struct _Cur
{
	int x;
	int y;
} Cur;

void init_map(char _map[Y][X])
{
	for (int i = 0; i < Y; ++i)
	{
		for (int j = 0; j < X; ++j)
		{
			_map[i][j] = 'a';
		}
	}
	_map[0][START] = 'b';
}

void print_map(char _map[Y][X])
{
	for (int i = 0; i < Y; ++i)
	{
		for (int j = 0; j < X; ++j)
		{
			if (_map[i][j] == 'a') printf("%s", "■");
			if (_map[i][j] == 'b') printf("%s", "□");
		}
		printf("\n");
	}
}

int main()
{
	int key = 0;
	Cur c = { START, 0 };
	char map[Y][X] = { 0 };
	init_map(map);
	while (1)
	{
		system("cls");
		print_map(map);
		key = getch();

		if (key == 224)
		{
			key = getch();
			switch (key) {
			case 72:        // 상
				if (c.y - 1 >= 0) c.y -= 1;
				map[c.y][c.x] = 'b';
				break;
			case 75:        // 좌
				if (c.x - 1 >= 0) c.x -= 1;
				map[c.y][c.x] = 'b';
				break;
			case 77:        // 우
				if (c.x + 1 < X) c.x += 1;
				map[c.y][c.x] = 'b';
				break;
			case 80:        // 하
				if (c.y + 1 < Y) c.y += 1;
				map[c.y][c.x] = 'b';
				break;
			default:
				break;
			}
		}
	}
	return 0;
}
```
