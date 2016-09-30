# 概要
　基本的なコード。

# コード
## オブジェクトの作成
1個のオブジェクト、1つのシェーダーの組を生成する場合。

### 宣言
```cpp
GLuint vao;
GLuint vbo;

GLfloat vertices[] = {
-0.5f, -0.5f, 0.0f,
 0.5f, -0.5f, 0.0f,
 0.0f,  0.5f, 0.0f
}; 

GLuint shaderProgram;
```

### 実装
```cpp
//シェーダー準備
//VS
GLuint vertexShader = glCreateShader(GL_VERTEX_SHADER);
glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);
glCompileShader(vertexShader);
{
	GLint success;
	GLchar infoLog[512];
	glGetShaderiv(vertexShader, GL_COMPILE_STATUS, &success);
	if(!success)
	{
		glGetShaderInfoLog(vertexShader, 512, NULL, infoLog);
		std::cout << "ERROR::SHADER::VERTEX::COMPILATION_FAILED\n" << infoLog << std::endl;
	}
}
//FS
GLuint fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);
glShaderSource(fragmentShader, 1, &fragmentShaderSource, NULL);
glCompileShader(fragmentShader);
{
	GLint success;
	GLchar infoLog[512];
	glGetShaderiv(fragmentShader, GL_COMPILE_STATUS, &success);
	if(!success)
	{
		glGetShaderInfoLog(fragmentShader, 512, NULL, infoLog);
		std::cout << "ERROR::SHADER::FRAGMENT::COMPILATION_FAILED\n" << infoLog << std::endl;
	}
}
//Program
shaderProgram = glCreateProgram();
glAttachShader(shaderProgram, vertexShader);
glAttachShader(shaderProgram, fragmentShader);
glLinkProgram(shaderProgram);
{
	GLint success;
	GLchar infoLog[512];
	glGetShaderiv(fragmentShader, GL_COMPILE_STATUS, &success);
	if(!success)
	{
		glGetShaderInfoLog(fragmentShader, 512, NULL, infoLog);
		std::cout << "ERROR::SHADER::FRAGMENT::COMPILATION_FAILED\n" << infoLog << std::endl;
	}
}
//もう使わない
glDeleteShader(vertexShader);
glDeleteShader(fragmentShader); 

glUseProgram(shaderProgram);

//モデルをバッファに詰める
//VAO
glGenVertexArrays(1, &vao); //第一引数でVAOの要素数を指定
//VAOをバインド、以下VAOにバインドしたいオブジェクトの属性が続く
glBindVertexArray(vao);
//VBO
glGenBuffers(1, &vbo);
glBindBuffer(GL_ARRAY_BUFFER, vbo);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

GLint positionLocation = glGetAttribLocation(m_basicLightingShader, "position");
glEnableVertexAttribArray(positionLocation);
glVertexAttribPointer(positionLocation, 3, GL_FLOAT, GL_FALSE, sizeof(GLfloat), (GLubyte*)nullptr);

//バインド解除
glBindBuffer(GL_ARRAY_BUFFER, 0);
glBindVertexArray(0);

```

## 描画
```cpp
glUseProgram(shaderProgram);
glBindVertexArray(VAO);
glDrawArrays(GL_TRIANGLES, 0, 3);
glBindVertexArray(0);  
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
