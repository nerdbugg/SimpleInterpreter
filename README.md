# �򵥺�����ͼ���Խ�����
------
���ֿ�Ϊ�����������רҵ����ԭ��γ̴���ҵ
------
## ��ҵҪ��
ʵ��һ��ָ���򵥺�����ͼ���ԵĽ����������Ը����Ա�д���ı�����Ϊ������ͼ��

## �ı�ʾ��
origin is (0,0);
scale is (10,10);
for T from 0 to 2*PI draw (cos(T), sin(T));

���������������(0,0)�����½�Ϊԭ�㣬����Ϊx��������Ϊy����ΪԲ�ģ��뾶Ϊ1��Բ���������x�᷽��y�᷽����Ŵ�10��

## �ʷ��Ǻ�

����Լ����

1. �ı��еĿո��ַ�Ĭ�ϱ�����������
2. �ı��Ĵ�Сд������
3. TԼ��Ϊ�﷨��һ��Ĭ�ϱ������﷨Ĭ��û���ṩ��������ķ�ʽ

�ؼ��֣�ORIGIN, FOR, ROT,  IS, SCALE, TO, T, STEP, DRAW

| �ʷ��Ǻ����� | ��Ӧ�ַ� |
| ------------ | -------- |
| COMMA        | ,        |
| SEMICO       | ;        |
| L_BRACKET    | (        |
| R_BRACKET    | )        |

## �����ķ�
------
### ԭʼ�ķ�

Program -> $\varepsilon$ | Program Statement SEMICO

Statement -> OriginStatement | ScaleStatement | RotStatement | ForStatement

OriginStatement -> ORIGIN IS L_BRACKET Expression COMMA Expression R_BRACKET

ScaleStatement -> SCALE IS L_BRACKET Expression COMMA Expression R_BRACKET

RotStatement -> ROT IS Expression

ForStatement -> FOR T FROM Expression TO Expression STEP Expression 
							DRAW L_BRACKET Expression COMMA Expression R_BRACKET

Expression -> Expression PLUS Expression | Expression MINUS Expression
            | Expression MUL Expression | Expression DIV Expression
            | PLUS Expression | MINUS Expression
            | Expression POWER Expression
            | CONST_ID
            | T
            | FUNC L_BRACKET
            | L_BRACKET Expression R_BRACKET


### ת�������ķ�
ͨ�����������ԣ���ݹ飬�����ӣ���д����ʽΪEBNF��ʽ������ָ�������ݹ��½�������ʵ��

Expression����ʽ���������ԣ�ͨ���������ȼ��ķ�ʽ

Expression -> Expression PLUS Term | Expression MINUS Term | Term

Term -> Term MUL Factor | Term DIV Factor | Factor

Factor -> PLUS Factor | MINUS Factor | Component

Component -> Atom POWER Component | Atom

Atom -> CONST_ID | T | FINC L_BRACKET Expression R_BRACKET 
        | L_BRACKET Expression R_BRACKET

### EBNF��ʽ�ķ�
������ݹ飬�����ӣ�����дΪEBNF��ʽ

Program -> {Statement SEMICO}//�ҵݹ飬���Ƴ����ַ����ȼ���ѭ��

Expression -> Term {(PLUS | MINUS) Term}

Term -> Factor {(MUL | DIV) Factor}

Factor -> {(PLUS | MINUS)} Factor | Component //Factor�ɸ�ΪComponent?

Component -> Atom [POWER Component] 

Atom -> CONST_ID | T | FINC L_BRACKET Expression R_BRACKET 
        | L_BRACKET Expression R_BRACKET

## �������

����dfa, Lexer, Parser, Painter�ĸ��࣬�ֱ����ȷ������״̬���ʷ����������﷨�����������������win32��ͼ�����ࡣͬʱ������Token��Tree�����࣬�ֱ�Ϊ�ʷ����ź��﷨���������ݽṹ��ģ����Ⱪ¶�ӿ����£�

```c++
class dfa
{
	vector<int> *states;//״̬����
	vector<int> *finalstates;//��̬����
	map<char, int> *matrix;//ת������ ���� ��ָ��
public:
	dfa();
	~dfa();
	int move(int state, char ch);//Ϊ�������ṩת�Ʋ�ѯ
	bool isFinal(int state);//�жϵ�ǰ״̬�Ƿ�Ϊ��̬
	int getInitial();//Ϊ�������ṩ��̬��ѯ
};

class Lexer
{
	fstream file;//�������ļ���
	dfa		*dfap;//���ص�dfa�����ָ��
public:
	Lexer(const wstring& fileName);
	Lexer(const string& fileName);
	~Lexer();
	Token* getToken();//�ṩ��ȡ��һ���ǺŵĽӿ�
};

class Parser
{
	Lexer* lexerp;//���õĴʷ�����������ָ��
	Token* curToken;//��ǰδƥ��ļǺŶ���ָ��
	Painter* painter; //��ͼ��������ָ��

	void Match(tokenType expect);//ƥ�䵥��ָ���Ǻ�
	//�ݹ��½��ӳ����ڽ����﷨����ʱ������Painter�����ͼ
	void Statement();
	void OriginStatement();
	void RotStatement();
	void ScaleStatement();
	void ForStatement();
	void ColorStatement();
	
	Tree* Expression();
	Tree* Term();
	Tree* Factor();
	Tree* Component();
	Tree* Atom();
public:
	void Program();//�ⲿ���õĽӿ�
	//���캯��������Ϊ�������ļ�����Painter����ָ��
	Parser(const wstring& fileName,Painter *p);
	Parser(const string& fileName, Painter* p);
	~Parser();
	//Token* fetchToken();
	Token* updateToken();//���µ�ǰ�ǺŵĽӿ�
};

class Painter
{
	HDC hdc;//win32�豸�����ľ��
	double origin_x, origin_y;//����ԭ��
	double scale_x, scale_y;//���ű���
	double rot;//��ת�Ƕ�
	unsigned red, green, blue;//��ͼ��ɫ
public:
	Painter(HDC h);
	//���û�ͼ���������Ľӿ�
	void setOrigin(double x, double y);//��������ԭ�㣬����Ϊ��������ϵ������
	void setScale(double x, double y);
	void setRot(double angle);
	void setColor(unsigned red, unsigned green, unsigned blue);
	void setColor(double red, double green, double blue);
	//�����߼������µĵ�
	void draw(double x, double y);
};

class Token
{
	string	word;//�Ǻ��ı�
	int row;//�Ǻ�����λ��
	funp funcp;//�������üǺŵ�ָ��
	double value;//�������Ǻŵ�ֵ
public:
	//���ڲ��ԣ���type��Ϊpublic
	tokenType type;
	Token(tokenType t,string w,int r);//��֪token����ʱ����
	Token(string w, int r);//ʶ��Ϊ��ʶ��ʱ����
	double getValue();//��ȡ�Ǻŵ�����ֵ
	int getRow();//��ȡ�Ǻ������к�
	funp getfuncp();//��ȡ�ǺŶ�Ӧ�ĺ���ָ��
	friend ostream& operator<<(ostream& o,Token t); //�������������
};

class Tree
{
	tokenType opType;//�﷨���������
	Tree* left, * right;//���Һ��ӽڵ�ָ��
	double rvalue;//�洢�ڵ����ֵ
	double* lvalue;//�洢�ڵ����ֵ
	funp funcp;//�������ýڵ�ĺ���ָ��
public:
	Tree(Token *t);
	~Tree();
	Tree* addLeft(Tree *t);//Ϊ��ǰ�ڵ��������
	Tree* addRight(Tree* t);//Ϊ��ǰ�ڵ�����Һ���
	double getValue();//����Ըýڵ�Ϊ���ı��ʽ����ֵ
	//test
	void printTree(int level);//��ӡ�﷨��
};

```

## �����Ľ�
1. �ɲ���Java Antlr���߸������ɳ���ʹ��Java Swing��ʵ�ֿ�ƽ̨��ͼ��
2. ����չ����ʹ�÷�ʽ�������Զ��������
3. ��ͨ������ʽ������չ�ʷ���������
