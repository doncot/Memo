# 概要
　基本的なコード。

# オブジェクトの作成
1個のオブジェクトを生成。

```cpp
//宣言
GLuint vao;
GLuint vbo;
GLfloat vertices[] = {
-0.5f, -0.5f, 0.0f,
 0.5f, -0.5f, 0.0f,
 0.0f,  0.5f, 0.0f
};  

//実装
//VAO
glGenVertexArrays(1, &vao); //第一引数でVAOの要素数を指定
//VAOをバインド、以下VAOにバインドしたいオブジェクトの属性が続く
glBindVertexArray(vao);
//VBO
glGenBuffers(1, &vbo);
glBindBuffer(GL_ARRAY_BUFFER, vbo);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);


```


# シェーダー
## 頂点シェーダー
```GLSL
#version 450 core

layout (location = 0) in vec3 position;

//variables
uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;

void main()
{
	gl_Position = projection * view * model * vec4(position, 1.0f);
}
```
## フラグメントシェーダー
```GLSL
#version 450 core

out vec4 color;

void main()
{
	color = vec4(1.0f, 0.5f, 0.2f, 1.0f);
} 
```

# 用語
| 用語 | 正式 | 説明 |
| --- | --- | --- |
| VBO | **V**ertex **B**uffer **O**bjects | 頂点をVRAM格納するバッファ |
| VAO | **V**ertex **A**rray **O**bject | aboを配列にしたもの | 属性（アトリビュート）とVBOの結びつきを保存できるバッファ |
