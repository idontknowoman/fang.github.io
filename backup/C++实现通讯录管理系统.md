用C++实现一个通讯录管理系统，实现以下功能:

1. 创建通讯录(包括姓名,性别,年龄,联系电话,家庭住址)

2. 显示通讯录(显示通讯录成员的基本信息)

3. 删除通讯录(删除通讯录中想删除的联系人)

4. 查找通讯录(按照联系人的名字查找到具体的联系人)

5. 修改通讯录(修改通讯录中联系人的基本信息)

6. 清空通讯录(删除通讯录中的所有联系人)

7. 退出通讯录(退出通讯录界面)



#### 考察知识点：

- struct结构体——定义、构造、初始化
- 枚举类型
- switch-case
- 数组操作
- 链表操作

#### 功能实现：

- 查询界面展示
- 增删查改



#### struct结构体

- struct结构体的定义

  - 代码如下：

    - ```c++
      struct Person {
          string name;
          Sex sex; // male or female
          int age;
          string phoneNumber;
          string address;
      
          Person() : name(""), sex(FEMALE), age(0), phoneNumber(""), address("") {};
          Person(string n, Sex s, int a, string pN, string addr) : name(n), sex(s), age(a), phoneNumber(pN), address(addr) {};
      };
      ```

    - 包含属性声明、方法声明、构造方法声明

    - struct与C++中类的区别

      - struct声明的结构体中的变量和方法的访问符都是public

  - 与C的不同

    - C在声明结构体时必须带struct，而C++中可以省略struct，这也是C中的结构体定义常使用typedef取别名的原因

  - 起别名方式

    - typedef（沿用C）

      - ```c++
        typedef struct Person {
            string name;
            Sex sex; // male or female
            int age;
            string phoneNumber;
            string address;
        
            Person() : name(""), sex(FEMALE), age(0), phoneNumber(""), address("") {};
            Person(string n, Sex s, int a, string pN, string addr) : name(n), sex(s), age(a), phoneNumber(pN), address(addr) {};
        } per;
        ```

    - 使用using（C++11及以后加入的新特性）

      - ```c++
        using per = struct Person;
        ```

- struct结构体的构造方法

  - 无参构造：`Person() : name(""), sex(FEMALE), age(0), phoneNumber(""), address("") {};`
  - 有参构造：`Person(string n, Sex s, int a, string pN, string addr) : name(n), sex(s), age(a), phoneNumber(pN), address(addr) {};`

- struct结构体的初始化

  - 直接在结构体定义的时候声明变量

    - ```c++
      struct Person {
          string name;
          Sex sex; // male or female
          int age;
          string phoneNumber;
          string address;
      
          Person() : name(""), sex(FEMALE), age(0), phoneNumber(""), address("") {};
          Person(string n, Sex s, int a, string pN, string addr) : name(n), sex(s), age(a), phoneNumber(pN), address(addr) {};
      } per;
      ```

  - 其他位置声明变量<!--(当Person结构体存在无参构造方法或Person结构体只包含基本类型时，可以直接用Person()初始化)-->

    - 兼容C: `struct Person person = struct Person()`
    - C++特性: `Person person = Person()`



#### 查询界面展示

输出提示，获取用户输入的选项，用户输入作为循环跳出条件（`swicth-case`处理各种情况）

代码如下：

```c++
	while (true) {
        cout << "********请选择功能: ********" << endl;
        // 1. 创建通讯录(包括姓名,性别,年龄,联系电话,家庭住址)
        cout << "******* 1.创建联系人 *******" << endl;
        // 2. 显示通讯录(显示通讯录成员的基本信息)
        cout << "******* 2.显示通讯录 *******" << endl;
        // 3. 删除通讯录(删除通讯录中想删除的联系人)
        cout << "******* 3.删除联系人 *******" << endl;
        // 4. 查找通讯录(按照联系人的名字查找到具体的联系人)
        cout << "******* 4.查找联系人 *******" << endl;
        // 5. 修改通讯录(修改通讯录中联系人的基本信息)
        cout << "******* 5.修改联系人 *******" << endl;
        // 6. 清空通讯录(删除通讯录中的所有联系人)
        cout << "******* 6.清空通讯录 *******" << endl;
        // 0. 退出通讯录(退出通讯录界面)
        cout << "******* 0.退出通讯录 *******" << endl;
        int choice;
        cin >> choice;
        switch (choice) {
            case 0:
                cout << "已退出通讯录系统" << endl;
                return 0;
            case 1:
                // 增加联系人
                addContact(contact);
                break;
            case 2:
                showContact(contact);
                break;
            case 3:
                deleteContact(contact);
                break;
            case 4:
                searchContact(contact);
                break;
            case 5:
                modifyContact(contact);
                break;
            case 6:
                clearContact(contact);
                break;
            default:
                cout << "选项不在功能内! 请重新选择" << endl;
                break;
        }
    }
```



#### 增删查改

对通讯录的管理可概括为增删查改功能，可采用以下两种数据结构实现

- 数组：
  - 优点：查找效率高
  - 缺点：删除效率低（后续元素都受到影响），容量固定
- 链表：
  - 优点：删除效率高（改变指针链接即可），容量不限，更灵活
  - 缺点：查找效率低



##### 数组实现的代码（完整版）：

```c++
#include <iostream>
using namespace std;

enum Sex{
    MALE,
    FEMALE
};

struct Person {
    string name;
    Sex sex; // male or female
    int age;
    string phoneNumber;
    string address;

    Person() : name(""), sex(FEMALE), age(0), phoneNumber(""), address("") {};
    Person(string n, Sex s, int a, string pN, string addr) : name(n), sex(s), age(a), phoneNumber(pN), address(addr) {};
};

// 数组存储
struct Contact
{
    struct Person person[1000];
    int cnt; // 存储通讯录中当前联系人数量

    Contact() : cnt(0) {};
};


int addContact(Contact& contact) {
    if (contact.cnt >= 1000) {
        cout << "通讯录已满，无法继续存储" << endl;
        return 0;
    }
    string name;
    cout << "请输入新增联系人姓名" << endl;
    cin >> name;
    int sex;
    cout << "请输入新增联系人性别(0表示男性, 1表示女性)" << endl;
    cin >> sex;
    while (sex != 0 && sex != 1) {
        cout << "输入不符合要求，请重新输入！" << endl;
        cin >> sex;
    }
    int age;
    cout << "请输入新增联系人年龄" << endl;
    cin >> age;
    string phoneNumber;
    cout << "请输入新增联系人联系电话" << endl;
    cin >> phoneNumber;
    string address;
    cout << "请输入新增联系人家庭住址" << endl;
    cin >> address;
    Person person(name, static_cast<Sex>(sex), age, phoneNumber, address);
    contact.person[contact.cnt] = person;
    contact.cnt++;
    return 1;
}

int showContact(Contact& contact) {
    cout << "通讯录列表如下:" << endl;
    for (int i = 0; i < contact.cnt; i++) {
        cout << "********************************" << endl;
        cout << "第" << i + 1 << "个联系人:" << endl;
        cout << "姓名" << contact.person[i].name << endl;
        cout << "性别" << contact.person[i].sex << endl;
        cout << "年龄" << contact.person[i].age << endl;
        cout << "联系电话" << contact.person[i].phoneNumber << endl;
        cout << "家庭住址" << contact.person[i].address << endl;
    }
    return 1;
}

int deleteContact(Contact& contact) {
    cout << "请输入你想删除的联系人姓名: " << endl;
    string name;
    cin >> name;
    for (int i = 0; i < contact.cnt; i++) {
        if (contact.person[i].name == name) {
            for (int j = i + 1; j < contact.cnt; j++) {
                contact.person[j-1] = contact.person[j];
            }
            contact.cnt--;
            return 1;
        }
    }
    cout << "未查找到该联系人" << endl;
    return 0;
}

int searchContact(Contact &contact) {
    cout << "请输入你想查找的联系人姓名: " << endl;
    string name;
    cin >> name;
    for (const Person& person : contact.person) {
        if (person.name == name) {
            cout << "姓名" << person.name << endl;
            cout << "性别" << person.sex << endl;
            cout << "年龄" << person.age << endl;
            cout << "联系电话" << person.phoneNumber << endl;
            cout << "家庭住址" << person.address << endl;
            return 1;
        }
    }
    cout << "未查找到该联系人" << endl;
    return 0;
}

int modifyContact(Contact& contact) {
    cout << "请输入你想修改的联系人姓名: " << endl;
    string name;
    cin >> name;
    for (Person & person : contact.person) {
        if (person.name == name) {
            int sex, age;
            string phoneNumber, address;
            cout << "请输入修改后的联系人性别(0表示男性, 1表示女性)" << endl;
            cin >> sex;
            while (sex != 0 && sex != 1) {
                cout << "输入不符合要求，请重新输入！" << endl;
                cin >> sex;
            }
            cout << "请输入修改后的联系人年龄" << endl;
            cin >> age;
            cout << "请输入修改后的联系人联系电话" << endl;
            cin >> phoneNumber;
            cout << "请输入修改后的联系人家庭住址" << endl;
            cin >> address;
            person.sex = static_cast<Sex>(sex);
            person.age = age;
            person.phoneNumber = phoneNumber;
            person.address = address;
            cout << "修改成功" << endl;
            return 1;
        }
    }
    cout << "未查找到该联系人" << endl;
    return 0;
}

int clearContact(Contact& contact) {
    contact.cnt = 0;
    cout << "通讯录已清空" << endl;
    return 1;
}

int main()
{
    cout << "****欢迎来到通讯录管理系统****" << endl;
    Contact contact;
    contact.cnt = 0;
    while (true) {
        cout << "********请选择功能: ********" << endl;
        // 1. 创建通讯录(包括姓名,性别,年龄,联系电话,家庭住址)
        cout << "******* 1.创建联系人 *******" << endl;
        // 2. 显示通讯录(显示通讯录成员的基本信息)
        cout << "******* 2.显示通讯录 *******" << endl;
        // 3. 删除通讯录(删除通讯录中想删除的联系人)
        cout << "******* 3.删除联系人 *******" << endl;
        // 4. 查找通讯录(按照联系人的名字查找到具体的联系人)
        cout << "******* 4.查找联系人 *******" << endl;
        // 5. 修改通讯录(修改通讯录中联系人的基本信息)
        cout << "******* 5.修改联系人 *******" << endl;
        // 6. 清空通讯录(删除通讯录中的所有联系人)
        cout << "******* 6.清空通讯录 *******" << endl;
        // 0. 退出通讯录(退出通讯录界面)
        cout << "******* 0.退出通讯录 *******" << endl;
        int choice;
        cin >> choice;
        switch (choice) {
            case 0:
                cout << "已退出通讯录系统" << endl;
                return 0;
            case 1:
                // 增加联系人
                addContact(contact);
                break;
            case 2:
                showContact(contact);
                break;
            case 3:
                deleteContact(contact);
                break;
            case 4:
                searchContact(contact);
                break;
            case 5:
                modifyContact(contact);
                break;
            case 6:
                clearContact(contact);
                break;
            default:
                cout << "选项不在功能内! 请重新选择" << endl;
                break;
        }
    }
}

```



##### 链表实现的代码（完整版）：

```c++
#include <iostream>
using namespace std;

enum Sex{
    MALE,
    FEMALE
};

struct Person {
    string name;
    Sex sex; // male or female
    int age;
    string phoneNumber;
    string address;

    Person() : name(""), sex(FEMALE), age(0), phoneNumber(""), address("") {};
    Person(string n, Sex s, int a, string pN, string addr) : name(n), sex(s), age(a), phoneNumber(pN), address(addr) {};
};

// 链表存储
struct Contact
{
    Person person;
    Contact* next;

    Contact() : person(), next(nullptr) {};
};


int addContact(Contact& contact) {
    string name;
    cout << "请输入新增联系人姓名" << endl;
    cin >> name;
    int sex;
    cout << "请输入新增联系人性别(0表示男性, 1表示女性)" << endl;
    cin >> sex;
    while (sex != 0 && sex != 1) {
        cout << "输入不符合要求，请重新输入！" << endl;
        cin >> sex;
    }
    int age;
    cout << "请输入新增联系人年龄" << endl;
    cin >> age;
    string phoneNumber;
    cout << "请输入新增联系人联系电话" << endl;
    cin >> phoneNumber;
    string address;
    cout << "请输入新增联系人家庭住址" << endl;
    cin >> address;
    Person person(name, static_cast<Sex>(sex), age, phoneNumber, address);
    Contact* newContact = new Contact;
    newContact->person = person;
    newContact->next = nullptr;
    Contact* p = &contact;
    while (p->next != nullptr) {
        p = p->next;
    }
    p->next = newContact;
    return 1;
}

int showContact(Contact& contact) {
    cout << "通讯录列表如下:" << endl;
    Contact* p = &contact;
    p = p->next;
    int i = 0;
    while(p!= nullptr) {
        cout << "********************************" << endl;
        cout << "第" << i + 1 << "个联系人:" << endl;
        cout << "姓名" << p->person.name << endl;
        cout << "性别" << p->person.sex << endl;
        cout << "年龄" << p->person.age << endl;
        cout << "联系电话" << p->person.phoneNumber << endl;
        cout << "家庭住址" << p->person.address << endl;
        p = p->next;
        i++;
    }
    return 1;
}

int deleteContact(Contact& contact) {
    cout << "请输入你想删除的联系人姓名: " << endl;
    string name;
    cin >> name;
    Contact* p = &contact;
    while (p->next != nullptr) {
        if (p->next->person.name == name) {
            Contact* temp = p->next;
            p->next = temp->next;
            delete temp;
            cout << "删除成功" << endl;
            return 1;
        }
        p = p->next;
    }
    cout << "未查找到该联系人" << endl;
    return 0;
}

int searchContact(Contact &contact) {
    cout << "请输入你想查找的联系人姓名: " << endl;
    string name;
    cin >> name;
    Contact* p = &contact;
    p = p->next;
    while (p != nullptr) {
        if (p->person.name == name) {
            cout << "姓名" << p->person.name << endl;
            cout << "性别" << p->person.sex << endl;
            cout << "年龄" << p->person.age << endl;
            cout << "联系电话" << p->person.phoneNumber << endl;
            cout << "家庭住址" << p->person.address << endl;
            return 1;
        }
        p = p->next;
    }
    cout << "未查找到该联系人" << endl;
    return 0;
}

int modifyContact(Contact& contact) {
    cout << "请输入你想修改的联系人姓名: " << endl;
    string name;
    cin >> name;
    Contact* p = &contact;
    p = p->next;
    while (p != nullptr) {
        if (p->person.name == name) {
            int sex, age;
            string phoneNumber, address;
            cout << "请输入修改后的联系人性别(0表示男性, 1表示女性)" << endl;
            cin >> sex;
            while (sex != 0 && sex != 1) {
                cout << "输入不符合要求，请重新输入！" << endl;
                cin >> sex;
            }
            cout << "请输入修改后的联系人年龄" << endl;
            cin >> age;
            cout << "请输入修改后的联系人联系电话" << endl;
            cin >> phoneNumber;
            cout << "请输入修改后的联系人家庭住址" << endl;
            cin >> address;
            p->person.sex = static_cast<Sex>(sex);
            p->person.age = age;
            p->person.phoneNumber = phoneNumber;
            p->person.address = address;
            cout << "修改成功" << endl;
            return 1;
        }
        p = p->next;
    }
    cout << "未查找到该联系人" << endl;
    return 0;
}

int clearContact(Contact& contact) {
    contact.next = nullptr;
    cout << "通讯录已清空" << endl;
    return 1;
}

int main()
{
    cout << "****欢迎来到通讯录管理系统****" << endl;
    Contact contact;
    while (true) {
        cout << "********请选择功能: ********" << endl;
        // 1. 创建通讯录(包括姓名,性别,年龄,联系电话,家庭住址)
        cout << "******* 1.创建联系人 *******" << endl;
        // 2. 显示通讯录(显示通讯录成员的基本信息)
        cout << "******* 2.显示通讯录 *******" << endl;
        // 3. 删除通讯录(删除通讯录中想删除的联系人)
        cout << "******* 3.删除联系人 *******" << endl;
        // 4. 查找通讯录(按照联系人的名字查找到具体的联系人)
        cout << "******* 4.查找联系人 *******" << endl;
        // 5. 修改通讯录(修改通讯录中联系人的基本信息)
        cout << "******* 5.修改联系人 *******" << endl;
        // 6. 清空通讯录(删除通讯录中的所有联系人)
        cout << "******* 6.清空通讯录 *******" << endl;
        // 0. 退出通讯录(退出通讯录界面)
        cout << "******* 0.退出通讯录 *******" << endl;
        int choice;
        cin >> choice;
        switch (choice) {
            case 0:
                cout << "已退出通讯录系统" << endl;
                return 0;
            case 1:
                // 增加联系人
                addContact(contact);
                break;
            case 2:
                showContact(contact);
                break;
            case 3:
                deleteContact(contact);
                break;
            case 4:
                searchContact(contact);
                break;
            case 5:
                modifyContact(contact);
                break;
            case 6:
                clearContact(contact);
                break;
            default:
                cout << "选项不在功能内! 请重新选择" << endl;
                break;
        }
    }
}
```