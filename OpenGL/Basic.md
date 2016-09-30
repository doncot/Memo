# 概要
　基本的なコード。

# オブジェクトの作成
```cpp
	//VAO(vertex array object)。aboを配列にしたもの
	glGenVertexArrays(3, m_VAOs); //第一引数でVAOの要素数を指定
	//VAOをバインド、以下VAOにバインドしたいオブジェクトが続く
	glBindVertexArray(m_VAOs[VAO_Index::Cube]);
	{
		glGenBuffers(1, &m_cubeVBO);
		glBindBuffer(GL_ARRAY_BUFFER, m_cubeVBO);
		glBufferData(GL_ARRAY_BUFFER, sizeof(cubeVertices), cubeVertices, GL_STATIC_DRAW);
	
	}
```


# シェーダー
## VS

## FS

# 用語
| 用語 | 正式 | 説明 |
| --- | --- | --- |
| VBO | **V**ertex **B**uffer **O**bjects | 頂点をVRAM格納するバッファ |
