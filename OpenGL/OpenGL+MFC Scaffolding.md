# 概要
OpenGL+MFCでネイティブ描画に最低限必要なコード。

# 実装（最低限）
## どっかのヘッダー

```cpp
#pragma comment(lib, "OpenGL32.lib")
#include<gl\GL.h>
#include<gl\GLU.h>

//絶対必須ではないが、下の部分で使う
#include<memory>
#include<stdexcept>
```

## CMyView.h

```cpp
private:
	afx_msg int OnCreate(LPCREATESTRUCT lpCreateStruct);
	afx_msg void OnSize(UINT nType, int cx, int cy);

	void SetDCPixelFormat(HDC hdc);

	///ClientDC
	std::shared_ptr<CDC> m_pDC;
	///rendering context
	HGLRC m_hGlRC;
```

## CMyView.cpp
### メッセーマップ
```cpp
BEGIN_MESSAGE_MAP(CRendererView, CView)
	//ここを追加
	ON_WM_CREATE()
	ON_WM_SIZE()
END_MESSAGE_MAP()
```

### OnCreate

```cpp
int CMyView::OnCreate(LPCREATESTRUCT lpCreateStruct)
{
	if (CView::OnCreate(lpCreateStruct) == -1)
		return -1;

	// TODO:  ここに特定な作成コードを追加してください。
	try
	{
		m_pDC = std::make_shared<CClientDC>(this);
		SetDCPixelFormat(m_pDC->GetSafeHdc());
		m_hGlRC = ::wglCreateContext(m_pDC->GetSafeHdc());
		wglMakeCurrent(m_pDC->GetSafeHdc(), m_hGlRC);

		glClearColor(0.0f, 0.2f, 0.4f, 1.0f);
		glClear(GL_COLOR_BUFFER_BIT);
	}
	catch(const std::exception& e)
	{
		CString value(e.what());
		::AfxMessageBox(value, MB_OK | MB_ICONERROR);
		SendMessage(WM_CLOSE);
	}
	return 0;
}
```

### OnSize

```cpp
void CMyView::OnSize(UINT nType, int cx, int cy)
{
	CView::OnSize(nType, cx, cy);

	// TODO: ここにメッセージ ハンドラー コードを追加します。
	glViewport (0, 0, cx , cy);
}
```

### SetDCPixelFormat

```cpp
void CMyView::SetDCPixelFormat(HDC hdc)
{
	//ピクセルフォーマットのデスクリプタ
	//マルチサンプリングをする場合はウィンドウの物を使うので、この値は上書きされる
	PIXELFORMATDESCRIPTOR pfd =
	{
		sizeof(PIXELFORMATDESCRIPTOR),         // Specifies the size of this data structure
		1,                                      // Specifies the version of this data structure
		PFD_DRAW_TO_WINDOW |                    // ピクセルバッファのビットフラグの設定
		PFD_SUPPORT_OPENGL |
		PFD_DOUBLEBUFFER,
		PFD_TYPE_RGBA,                        // RGBA pixel values
		32,                                   // 32-bitカラーと指定
		0, 0, 0, 0, 0, 0,                     // Specifies the number of red bitplanes in each RGBA color buffer
		0, 0,                                 // Specifies the number of alpha bitplanes in each RGBA color buffer
		0, 0, 0, 0, 0,                        // Specifies the total number of bitplanes in the accumulation buffer
		32,                                   // Specifies the depth(bit) of the depth (z-axis) buffer
		32,                                   // Specifies the depth of the stencil buffer
		0,                                    // Specifies the number of auxiliary buffers
		PFD_MAIN_PLANE,                       // Layer type　Ignored...
		0,                                    // Specifies the number of overlay and underlay planes
		0,                                    // Ignored
		0,                                    // Specifies the transparent color or index of an underlay plane
		0                                     // Ignored
	};

	//pixelフォーマット
	int pixelFormatIndex = ChoosePixelFormat(hdc, &pfd);

	//実際のDCにピクセルフォーマットを設定する
	if (!SetPixelFormat(hdc, pixelFormatIndex, &pfd))
	{
		throw std::runtime_error("Failed to set pixel format");
	}

	if (DescribePixelFormat(hdc, pixelFormatIndex, sizeof(PIXELFORMATDESCRIPTOR), &pfd) == 0)
	{
		throw std::runtime_error("the device context's pixel format is invalid");
	}
}
```

### OnDraw

```cpp
void CLicView::OnDraw(CDC* /*pDC*/)
{
	CLicDoc* pDoc = GetDocument();
	ASSERT_VALID(pDoc);
	if (!pDoc)
		return;

	// TODO: この場所にネイティブ データ用の描画コードを追加します。
	SwapBuffers(m_pDC->m_hDC);
}
```

## 実装（三角形の描画まで）

### GLEW

1. [公式](http://glew.sourceforge.net/)からとってくる。
2. IncludeとLibのパスを通す。
3. glew32s.libを追加する。
4. glewの初期化コードを追加。

```cpp
//GLEW初期化コード
glewExperimental = GL_TRUE;
if (glewInit() != GLEW_OK)
{
	throw std::runtime_error("Failed to initialize GLEW");
}
```

### リソースの宣言
ヘッダー辺りに

```cpp
GLuint shaderProgram;
GLuint vbo;
GLuint vao;
```

### 三角形リソース構築

```cpp
GLfloat vertices[] = {
	-0.5f, -0.5f, 0.0f,
	0.5f, -0.5f, 0.0f,
	0.0f,  0.5f, 0.0f
};
glGenVertexArrays(1, &vao);
glGenBuffers(1, &vbo);
glBindVertexArray(vao);
	glBindBuffer(GL_ARRAY_BUFFER, vbo);
		glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
		glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(GLfloat), (GLvoid*)0);
		glEnableVertexAttribArray(0);
	glBindBuffer(GL_ARRAY_BUFFER, 0);
glBindVertexArray(0);
```

### シェーダー構築

``cpp
GLuint CreateShader(const GLchar * vsSource, const GLchar * fsSource)
{
	const size_t BuffSize = 8192;

	//VS
	GLuint vertexShader = glCreateShader(GL_VERTEX_SHADER);
	glShaderSource(vertexShader, 1, &vsSource, NULL);
	glCompileShader(vertexShader);
	{
		GLint success;
		GLchar infoLog[BuffSize] = "";
		glGetShaderiv(vertexShader, GL_COMPILE_STATUS, &success);
		if (!success)
		{
			glGetShaderInfoLog(vertexShader, BuffSize, NULL, infoLog);
			throw std::runtime_error(std::string("ERROR::SHADER::VERTEX::COMPILATION_FAILED\n") + std::string(infoLog));
		}
	}

	//FS
	GLuint fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);
	glShaderSource(fragmentShader, 1, &fsSource, NULL);
	glCompileShader(fragmentShader);
	{
		GLint success;
		GLchar infoLog[BuffSize] = "";
		glGetShaderiv(fragmentShader, GL_COMPILE_STATUS, &success);
		if (!success)
		{
			glGetShaderInfoLog(fragmentShader, BuffSize, NULL, infoLog);
			throw std::runtime_error(std::string("ERROR::SHADER::FRAGMENT::COMPILATION_FAILED\n") + std::string(infoLog));
		}
	}

	//Program
	GLuint shader = glCreateProgram();
	glAttachShader(shader, vertexShader);
	glAttachShader(shader, fragmentShader);
	glLinkProgram(shader);
	{
		GLint success;
		GLchar infoLog[BuffSize] = "";
		glGetProgramiv(shader, GL_LINK_STATUS, &success);
		if (!success)
		{
			glGetProgramInfoLog(shader, BuffSize, NULL, infoLog);
			throw std::runtime_error("ERROR::SHADER::PROGRAM::LINKING_FAILED" + std::string(infoLog));
		}
	}
	//もう使わない
	glDeleteShader(vertexShader);
	glDeleteShader(fragmentShader);

	return shader;
}
```
* おまけ―テキストファイル読み込み

```cpp
#include<fstream>
#include<string>

std::ifstream vsfile("basic.vert");
std::string vsSource;
std::string buff;
while (std::getline(vsfile, buff))
{
	vsSource += buff + std::string("\n");
}
```

### 頂点シェーダー

```glsl
#version 330 core
  
layout (location = 0) in vec3 position;

void main()
{
    gl_Position = vec4(position.x, position.y, position.z, 1.0);
}
```

### フラグメントシェーダー

```glsl
#version 330 core

out vec4 color;

void main()
{
    color = vec4(1.0f, 0.5f, 0.2f, 1.0f);
}
```
