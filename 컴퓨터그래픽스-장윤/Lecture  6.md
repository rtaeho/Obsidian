c##### Coordinate
- object
- camera
- window
- world

##### camera 세팅
- glMatrixMode(GL_PROJECTION) -> 카메라 렌즈 세팅
- glLoadIdentity() -> 초기화
- glOrtho(left, right, bottom, top, near, far) -> x, y, z축 설정

##### Object 세팅
- point
- line 
- triangle
- quad
- polygon
	- simple : 간선끼리 교차하지 않아야함
	- convex : 가운데를 기준으로 모든 꼭짓점은 밖으로 뻗어야함
	- flat : 한 평면상에 있어야함

