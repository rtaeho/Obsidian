#### OpenGL Matrix
- glMatrixMode(GL_PROJECTION) : 카메라 세팅
- glMatrixMode(GL_MODELVIEW) : 그 외의 모든 것

#### Matrix Stacks 
``` C
glMatrixMode(GL_MODELVIEW);
glLoadIdentity();
glTranslatef(1.0, 2.0, 3.0);
glRotatef(30.0, 0.0, 0.0, 1.0);
glTranslatef(-1.0, -2.0, -3.0);
```
- glPushMatrix() : glLoadIdentity()아래에 위치
	- pop 한 후 다시 glLoadIdentity 실행
- glPopMatrix() : object 그린 후 위치
