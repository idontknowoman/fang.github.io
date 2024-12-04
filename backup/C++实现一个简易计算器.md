用C++实现一个简易计算器，实现小数的加减乘除计算的基本功能
#### 考察知识点：
- 输入输出
- 栈
- 异常处理
- 类型转换
- 函数传参

#### 功能实现：
- 中缀表达式转后缀表达式
- 后缀表达式计算

#### 中缀表达式转后缀表达式
首先获取输入的中缀表达式字符串，实现以下到后缀表达式的转换，如：
`3 + 5 * 10 ——> 3 5 10 * +`
那么这里需要准备一个栈，控制操作符的进出顺序，操作数的顺序没有改变，直接保留即可
流程：
1. 定义一个指针，遍历中缀表达式字符串，遇到操作数，直接添加到结果
2. 遇到操作符，则判断情况，优先级大的操作符直接进栈
    1. 操作符为'(' or '*' or '\'：直接进栈（`stack.push(op)`）
    2. 操作符为'+' or '-': 一直出栈（`stack.pop()`），加入结果中的后缀表达式，直到栈顶（`stack.top()`）元素的操作符优先级不高于当前op
    3. 操作符为')'：一直出栈，，加入结果中的后缀表达式，直到栈顶元素为'('，出栈，不加入结果
3. 遍历结束，弹出所有栈中元素（循环终止条件`stack.empty()`）

#### 后缀表达式的计算
维护一个操作数栈，
流程：
1. 定义一个指针，从左至右依次读取后缀表达式的元素，遇到操作数就进栈
2. 遇到操作符就计算（`switch-case`加减乘除四种情况），将栈顶两元素的值计算后重新压栈


#### 异常处理
**除0异常**:自定义异常`throw std::invalid_argument("Division by zero")`，需包含`<stdexcept>`头文件

#### 类型转换
- `char`转`int`: `int(ch-'0')`即可
- `int`转`double`: 直接强制类型转换

#### 函数传参
- 传值: 函数内部对参数的修改不会影响外部
- 传址: 函数参数中携带&标识，标识传值，函数对该参数的修改是全局的

```
#include<iostream>
#include <stack>
#include <cmath>
#include <iomanip>
#include <sstream>
using namespace std;

std::string double_to_string(double value) {
    // 使用ostringstream创建字符串流
    ostringstream out;
    // 设置fixed格式以避免科学计数法
    out << fixed << setprecision(10) << value; // 设置足够多的小数位，之后再处理
    std::string result = out.str();
    // 查找最后一个小数点的位置
    size_t pos = result.find('.');
    if (pos != string::npos) {
        // 去除小数点后无效的0
        result.erase(result.find_last_not_of('0') + 1, string::npos);
        // 如果小数点是最后一个字符，去掉小数点
        if (result.back() == '.') {
            result.pop_back();
        }
    }
    return result;
}

double getnum(string& input, int& i) {
    int num = int(input[i] - '0');
    i++;
    while (i < input.length() && isdigit(input[i])) {
        num = num * 10 + int(input[i] - '0');
        i++;
    }
    double xiaoshu = 0;
    int index = 1;
    if (input[i] == '.') {
        i++;
        while (i < input.length() && isdigit(input[i])) {
            xiaoshu += (input[i] - '0') * pow(0.1, index);
            i++;
        }
    }
    double operand = num + xiaoshu;
    return operand;
}

// 中缀表达式转后缀表达式
string infix_to_postfix(string& input) {
    string output = "";
    stack<char> operators;
    // 3 + 5 * 10 ——> 3 5 10 * +
    for (int i = 0; i < input.length(); i++) {
        char c = input[i];
        if (isdigit(c)) {
            double operand = getnum(input, i);
            output += double_to_string(operand) + " ";
            c = input[i];
        }
        if (c == '(' || c == '*' || c == '/') {
            operators.push(c);
        } else if (c == ')') {
            while (!operators.empty() && operators.top() != '(') {
                output += string(1, operators.top()) + " ";
                operators.pop();
            }
            if (!operators.empty() && operators.top() == '(') {
                operators.pop();
            }
        } else if (c == '+' || c == '-') {
            while (!operators.empty() && operators.top() != '(' && (operators.top() == '*' || operators.top() == '/')) {
                output += string(1, operators.top()) + " ";
                operators.pop();
            }
            operators.push(c);
        }
    }
    while (!operators.empty()) {
        output += string(1, operators.top()) + " ";
        operators.pop();
    }
    return output;
}

double calc(string& postfix_expression) {
    stack<double> operands;
    for (int i = 0; i < postfix_expression.length(); i++) {
        char c = postfix_expression[i];
        if (isdigit(c)) {
            double operand = getnum(postfix_expression, i);
            operands.push(operand);
            c = postfix_expression[i];
        }
        double op1, op2;
        if (c == '+' || c == '-' || c == '*' || c == '/') {
            op1 = operands.top();
            operands.pop();
            op2 = operands.top();
            operands.pop();
        } else {
            continue;
        }
        switch (c) {
            case '+':
                operands.push(op1 + op2);
                break;
            case '-':
                operands.push(op1 - op2);
                break;
            case '*':
                operands.push(op1 * op2);
                break;
            case '/': {
                if (op2 == 0) {
                    cout << "Error: division by zero" << endl;
                    return 0;
                }
                operands.push(op1 / op2);
                break;
            }
        }
    }
    return operands.top();
}

// 实现一个计算器
int main() {
    string input;
    while (true) {
        cout << "Enter an expression: " << endl;
        getline(cin, input);
        if (input == "exit") {
            break;
        }
        string new_expression = infix_to_postfix(input);
        // 打印一下中间结果：后缀表达式
        cout << "Postfix expression: " << new_expression << endl;
        // 根据后缀表达式计算结果
        double result = calc(new_expression);
        cout << "Result: " << result << endl;
    }
}
```