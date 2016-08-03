# 概要
OpenGL+MFCでネイティブ描画に最低限必要なコード。

# 実装
## どっかのヘッダー

```cpp
#pragma comment(lib, "OpenGL32.lib")
#include<gl\GL.h>
#include<gl\GLU.h>

#include<memory>
#include<stdexcept>
```

## CMyView.h

```cpp
afx_msg int OnCreate(LPCREATESTRUCT lpCreateStruct);
afx_msg void OnSize(UINT nType, int cx, int cy);

///ClientDC
std::shared_ptr<CDC> m_pDC;
///rendering context
HGLRC m_hGlRC;
```

## CMyView.cpp
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
	catch(const exception& e)
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
	//pixelフォーマット
	int pixelFormatIndex = -1;

	//ピクセルフォーマットのデスクリプタ
	//マルチサンプリングをする場合はウィンドウの物を使うので、この値は上書きされる
    PIXELFORMATDESCRIPTOR pfd = 
	{
        sizeof (PIXELFORMATDESCRIPTOR), 		// Specifies the size of this data structure
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

	//マルチサンプリング用にピクセルフォーマットを取得してなければ、今取得する
	if(pixelFormatIndex == -1)
	{
		pixelFormatIndex = ChoosePixelFormat(hdc, &pfd);
	}

	//実際のDCにピクセルフォーマットを設定する
    if(!SetPixelFormat(hdc, pixelFormatIndex, &pfd))
	{
		throw runtime_error("Failed to set pixel format");
    }

    if(DescribePixelFormat(hdc, pixelFormatIndex, sizeof(PIXELFORMATDESCRIPTOR), &pfd) == 0)
	{
		throw runtime_error("the device context's pixel format is invalid");
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
