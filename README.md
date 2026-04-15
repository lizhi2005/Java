学生管理系统（升级版）项目说明
📌 项目概述
这是一个基于 Java 实现的学生管理系统，核心功能包含用户注册、登录、忘记密码，并实现了登录成功后才能进入学生管理系统进行增删改查操作的权限控制，符合 Java 面向对象编程规范，适合作为 Java 入门级完整项目。
📁 项目结构
plaintext
first-full-project/
├── Studentutil/                  # 核心业务包
│   ├── Student.java              # 学生实体类
│   ├── AuthService.java          # 认证服务类（登录/注册/重置密码）
│   └── StudentManagement.java    # 系统主入口+管理菜单
├── .git/                          # Git版本控制目录
├── .idea/                         # IDEA项目配置目录
├── out/                           # 编译输出目录
├── d1-code/                       # 其他代码目录
├── demo1/                         # 示例目录
└── first-full-project.iml         # IDEA项目配置文件
✨ 核心功能
1. 认证模块（AuthService）
用户注册：校验用户名唯一性、密码复杂度（3-15 位，字母 + 数字）、身份证号（18 位，非 0 开头，最后一位可 X/x）、手机号（11 位纯数字，非 0 开头）
用户登录：用户名密码校验 + 图形验证码（5 位，字母 + 数字混合），3 次错误机会锁定
忘记密码：通过用户名 + 手机号 + 身份证号验证身份，重置新密码
2. 学生管理模块（StudentManagement）
仅登录成功后可进入，支持：
查看所有学生信息
添加新学生
修改学生密码
删除学生
返回主菜单
3. 系统主菜单
登录
注册
忘记密码
进入学生管理系统（需登录）
退出系统
🧩 核心类说明
1. Student.java（学生实体类）
封装学生核心属性，提供 get/set 方法：
java
运行
package Studentutil;

public class Student {
    private String username;   // 用户名
    private String password;   // 密码
    private String password2;  // 确认密码（注册用）
    private String id;         // 身份证号
    private String phonenumber;// 手机号

    // 全量getter/setter方法
    public String getUsername() { return username; }
    public void setUsername(String username) { this.username = username; }
    // 其他属性的get/set方法省略
}
2. AuthService.java（认证服务类）
核心方法：
register(ArrayList<Student> students)：用户注册，全量校验
login(ArrayList<Student> students)：用户登录，返回登录结果
resetPassword(ArrayList<Student> students)：重置密码
generateVerificationCode()：生成 5 位随机验证码
3. StudentManagement.java（系统主入口）
初始化 5 个测试学生数据
主菜单循环，控制登录状态
studentManagerMenu()：学生管理系统菜单，实现增删改查
🚀 运行说明
环境要求
JDK 8 及以上
IntelliJ IDEA（推荐）
运行步骤
打开 IDEA，导入first-full-project项目
找到Studentutil.StudentManagement类，右键运行main方法
按控制台提示操作：
测试账号：shentang / St123456（已初始化）
登录成功后自动进入学生管理系统
可自行注册新用户、重置密码、管理学生信息
🐛 已知问题 & 修复说明
已修复的核心逻辑问题
登录状态不生效：修改login方法返回布尔值，登录成功后标记isLoggedIn=true
登录后退回主菜单：登录成功后直接跳转管理系统菜单
重置密码逻辑错误：修复用户名校验、身份验证逻辑
验证码死循环：验证码错误扣次数，3 次锁定
编译错误：修复IO.println为System.out.println
📝 使用示例
plaintext
欢迎进入学生管理系统
学生数据初始化完成！

====== 主菜单 ======
1. 登录
2. 注册
3. 忘记密码
4. 进入学生管理系统（需登录）
5. 退出
请选择：1
请输入用户名：shentang
请输入密码：St123456
验证码：1gdoB
请输入验证码：1gdoB
Login successful
✅ 登录成功！正在进入学生管理系统...

====== 欢迎进入学生管理系统 ======
1. 查看所有学生
2. 添加学生
3. 修改学生密码
4. 删除学生
5. 返回主菜单
请选择操作：1
====== 学生列表 ======
用户名：shentang 手机号：13800138001 身份证：110101199901011234
用户名：qishan 手机号：13800138002 身份证：110101199902022345
...
📌 扩展建议
增加数据持久化：将学生信息存入文件 / 数据库，避免重启数据丢失
优化密码存储：使用 MD5 加密存储密码，避免明文存储
增加管理员权限：区分普通用户和管理员，管理员可管理所有用户
优化界面：使用 Swing/JavaFX 实现图形化界面
📄 完整最终版代码（核心类）
AuthService.java（最终修复版）
java
运行
package Studentutil;

import java.util.ArrayList;
import java.util.Random;
import java.util.Scanner;

public class AuthService {
    public boolean register(ArrayList<Student> students) {
        Student student = new Student();
        Scanner sc = new Scanner(System.in);

        System.out.println("请输入用户名：");
        student.setUsername(sc.nextLine());
        System.out.println("请输入密码：");
        student.setPassword(sc.nextLine());
        System.out.println("请输入确认密码：");
        student.setPassword2(sc.nextLine());
        System.out.println("请输入身份证号：");
        student.setId(sc.nextLine());
        System.out.println("请输入手机号：");
        student.setPhonenumber(sc.nextLine());

        // 用户名重复校验
        for (Student s : students) {
            if (s.getUsername().equals(student.getUsername())) {
                System.out.println("Username already exists");
                return false;
            }
        }

        // 密码长度校验
        String pwd = student.getPassword();
        if (pwd.length() > 15 || pwd.length() < 3) {
            System.out.println("Password length must be between 3 and 15 characters");
            return false;
        }

        // 密码必须包含字母+数字
        int countnum = 0, countalpht = 0;
        for (char c : pwd.toCharArray()) {
            if ((c >= 'a' && c <= 'z') || (c >= 'A' && c <= 'Z')) {
                countalpht++;
            } else if (c >= '0' && c <= '9') {
                countnum++;
            }
        }
        if (countnum == 0 || countalpht == 0) {
            System.out.println("Password must contain both letters and numbers");
            return false;
        }

        // 两次密码一致校验
        if (!student.getPassword2().equals(pwd)) {
            System.out.println("Password and confirm password must be the same");
            return false;
        }

        // 身份证校验
        String userid = student.getId();
        if (userid.length() != 18) {
            System.out.println("ID must be 18 characters long");
            return false;
        }
        if (userid.charAt(0) == '0') {
            System.out.println("ID must start with not 0");
            return false;
        }
        for (int i = 0; i < userid.length(); i++) {
            char c = userid.charAt(i);
            if (i < 17) {
                if (c < '0' || c > '9') {
                    System.out.println("ID must contain only numbers");
                    return false;
                }
            } else {
                if (!((c >= '0' && c <= '9') || c == 'X' || c == 'x')) {
                    System.out.println("ID must contain only numbers or X at the end");
                    return false;
                }
            }
        }

        // 手机号校验
        String phonenum = student.getPhonenumber();
        if (phonenum.length() != 11) {
            System.out.println("Phone number must be 11 characters long");
            return false;
        }
        for (int i = 0; i < phonenum.length(); i++) {
            char c = phonenum.charAt(i);
            if (c < '0' || c > '9') {
                System.out.println("Phone number must contain only numbers");
                return false;
            }
            if (i == 0 && c == '0') {
                System.out.println("Phone number must start with not 0");
                return false;
            }
        }

        students.add(student);
        System.out.println("Register successful");
        return true;
    }

    private String generateVerificationCode() {
        char[] arr = new char[52];
        for (int i = 0; i < 26; i++) {
            arr[i] = (char) ('A' + i);
            arr[i + 26] = (char) ('a' + i);
        }
        Random r = new Random();
        StringBuilder sb = new StringBuilder();
        // 4个字母+1个数字
        for (int i = 0; i < 4; i++) {
            sb.append(arr[r.nextInt(arr.length)]);
        }
        sb.append(r.nextInt(10));
        // 打乱顺序
        char[] codeArr = sb.toString().toCharArray();
        for (int i = 0; i < codeArr.length; i++) {
            int j = r.nextInt(codeArr.length);
            char temp = codeArr[i];
            codeArr[i] = codeArr[j];
            codeArr[j] = temp;
        }
        return new String(codeArr);
    }

    public boolean login(ArrayList<Student> students) {
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入用户名：");
        String username = sc.nextLine();
        System.out.println("请输入密码：");
        String password = sc.nextLine();

        // 校验用户名是否存在
        boolean userExists = false;
        for (Student s : students) {
            if (s.getUsername().equals(username)) {
                userExists = true;
                break;
            }
        }
        if (!userExists) {
            System.out.println("Username not found");
            System.out.println("用户名未注册，请先注册");
            return false;
        }

        int attempts = 3;
        while (attempts > 0) {
            String verificationCode = generateVerificationCode();
            System.out.println("验证码：" + verificationCode);
            System.out.print("请输入验证码：");
            String inputCode = sc.nextLine();

            if (!inputCode.equalsIgnoreCase(verificationCode)) {
                attempts--;
                System.out.println("验证码不正确，剩余尝试次数：" + attempts);
                continue;
            }

            // 校验密码
            boolean loginSuccess = false;
            for (Student s : students) {
                if (s.getUsername().equals(username) && s.getPassword().equals(password)) {
                    System.out.println("Login successful");
                    return true;
                }
            }

            attempts--;
            if (attempts > 0) {
                System.out.println("密码错误，剩余尝试次数：" + attempts);
            } else {
                System.out.println("密码错误，登录失败");
            }
        }
        return false;
    }

    public void resetPassword(ArrayList<Student> students) {
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入用户名：");
        String username = sc.nextLine();

        // 查找目标用户
        Student target = null;
        for (Student s : students) {
            if (s.getUsername().equals(username)) {
                target = s;
                break;
            }
        }
        if (target == null) {
            System.out.println("Username not found");
            System.out.println("用户名未注册，请先注册");
            return;
        }

        // 验证手机号和身份证
        System.out.println("请输入手机号：");
        String phone = sc.nextLine();
        System.out.println("请输入身份证号：");
        String id = sc.nextLine();

        if (!target.getPhonenumber().equals(phone) || !target.getId().equals(id)) {
            System.out.println("Phone number or ID card not found");
            System.out.println("手机号或身份证号不正确");
            return;
        }

        // 重置密码
        System.out.println("请输入新密码：");
        String newPwd = sc.nextLine();
        System.out.println("请输入确认新密码：");
        String confirmPwd = sc.nextLine();

        if (!newPwd.equals(confirmPwd)) {
            System.out.println("New password and confirm password do not match");
            System.out.println("新密码和确认新密码不一致");
            return;
        }

        target.setPassword(newPwd);
        System.out.println("Reset password successful");
        System.out.println("密码重置成功");
    }
}
StudentManagement.java（最终修复版）
java
运行
package Studentutil;

import java.util.ArrayList;
import java.util.Scanner;

public class StudentManagement {
    public static void main(String[] args) {
        main();
    }

    static void main() {
        Scanner sc = new Scanner(System.in);
        System.out.println("欢迎进入学生管理系统");
        ArrayList<Student> students = new ArrayList<>();

        // 初始化测试学生数据
        Student stu1 = new Student();
        stu1.setUsername("shentang");
        stu1.setPassword("St123456");
        stu1.setId("110101199901011234");
        stu1.setPhonenumber("13800138001");

        Student stu2 = new Student();
        stu2.setUsername("qishan");
        stu2.setPassword("Qs123456");
        stu2.setId("110101199902022345");
        stu2.setPhonenumber("13800138002");

        Student stu3 = new Student();
        stu3.setUsername("gongxichou");
        stu3.setPassword("Gxc123456");
        stu3.setId("110101199903033456");
        stu3.setPhonenumber("13800138003");

        Student stu4 = new Student();
        stu4.setUsername("chuwuhui");
        stu4.setPassword("Cwh123456");
        stu4.setId("110101199904044567");
        stu4.setPhonenumber("13800138004");

        Student stu5 = new Student();
        stu5.setUsername("linfeng");
        stu5.setPassword("Lf123456");
        stu5.setId("110101199905055678");
        stu5.setPhonenumber("13800138005");

        students.add(stu1);
        students.add(stu2);
        students.add(stu3);
        students.add(stu4);
        students.add(stu5);
        System.out.println("学生数据初始化完成！");

        AuthService authService = new AuthService();
        boolean isLoggedIn = false;

        while (true) {
            System.out.println("\n====== 主菜单 ======");
            System.out.println("1. 登录");
            System.out.println("2. 注册");
            System.out.println("3. 忘记密码");
            System.out.println("4. 进入学生管理系统（需登录）");
            System.out.println("5. 退出");
            System.out.print("请选择：");
            int choice = sc.nextInt();
            sc.nextLine();

            switch (choice) {
                case 1:
                    boolean success = authService.login(students);
                    if (success) {
                        isLoggedIn = true;
                        System.out.println("✅ 登录成功！正在进入学生管理系统...\n");
                        studentManagerMenu(students, sc);
                    } else {
                        isLoggedIn = false;
                    }
                    break;
                case 2:
                    authService.register(students);
                    break;
                case 3:
                    authService.resetPassword(students);
                    break;
                case 4:
                    if (isLoggedIn) {
                        System.out.println("====== 欢迎进入学生管理系统 ======");
                        studentManagerMenu(students, sc);
                    } else {
                        System.out.println("❌ 请先登录！");
                    }
                    break;
                case 5:
                    System.out.println("谢谢使用！");
                    return;
                default:
                    System.out.println("无效选择，请重新输入！");
                    break;
            }
        }
    }

    private static void studentManagerMenu(ArrayList<Student> students, Scanner sc) {
        while (true) {
            System.out.println("\n====== 学生管理系统 ======");
            System.out.println("1. 查看所有学生");
            System.out.println("2. 添加学生");
            System.out.println("3. 修改学生密码");
            System.out.println("4. 删除学生");
            System.out.println("5. 返回主菜单");
            System.out.print("请选择操作：");
            int op = sc.nextInt();
            sc.nextLine();

            switch (op) {
                case 1:
                    System.out.println("\n====== 学生列表 ======");
                    for (Student s : students) {
                        System.out.printf("用户名：%s | 手机号：%s | 身份证：%s%n",
                                s.getUsername(), s.getPhonenumber(), s.getId());
                    }
                    break;
                case 2:
                    Student newStu = new Student();
                    System.out.print("请输入用户名：");
                    newStu.setUsername(sc.nextLine());
                    System.out.print("请输入密码：");
                    newStu.setPassword(sc.nextLine());
                    System.out.print("请输入身份证号：");
                    newStu.setId(sc.nextLine());
                    System.out.print("请输入手机号：");
                    newStu.setPhonenumber(sc.nextLine());
                    students.add(newStu);
                    System.out.println("✅ 添加学生成功！");
                    break;
                case 3:
                    System.out.print("请输入要修改密码的用户名：");
                    String updateName = sc.nextLine();
                    boolean found = false;
                    for (Student s : students) {
                        if (s.getUsername().equals(updateName)) {
                            System.out.print("请输入新密码：");
                            s.setPassword(sc.nextLine());
                            System.out.println("✅ 密码修改成功！");
                            found = true;
                            break;
                        }
                    }
                    if (!found) {
                        System.out.println("❌ 用户名不存在！");
                    }
                    break;
                case 4:
                    System.out.print("请输入要删除的用户名：");
                    String delName = sc.nextLine();
                    Student delStu = null;
                    for (Student s : students) {
                        if (s.getUsername().equals(delName)) {
                            delStu = s;
                            break;
                        }
                    }
                    if (delStu != null) {
                        students.remove(delStu);
                        System.out.println("✅ 删除学生成功！");
                    } else {
                        System.out.println("❌ 用户名不存在！");
                    }
                    break;
                case 5:
                    System.out.println("返回主菜单...\n");
                    return;
                default:
                    System.out.println("无效选择，请重新输入！");
                    break;
            }
        }
    }
}
## 学生管理系统升级版

### 需求：

​	为学生管理系统书写一个登陆、注册、忘记密码的功能。

​	只有用户登录成功之后，才能进入到学生管理系统中进行增删改查操作。

### 分析：

#### 登录界面：

```java
System.out.println("欢迎来到学生管理系统");
System.out.println("请选择操作1登录 2注册 3忘记密码");
```

#### 用户类：

​	属性：用户名、密码、身份证号码、手机号码

#### 注册功能：

​	1，用户名需要满足以下要求：

​		验证要求：

​			用户名唯一

​			用户名长度必须在3~15位之间 

​			只能是字母加数字的组合，但是不能是纯数字

​	2，密码键盘输入两次，两次一致才可以进行注册。

​	3，身份证号码需要验证。

​		验证要求：

​			长度为18位

​			不能以0为开头

​			前17位，必须都是数字

​			最为一位可以是数字，也可以是大写X或小写x

​	4，手机号验证。

​		验证要求：

​			长度为11位

​			不能以0为开头

​			必须都是数字

#### 登录功能：

​	1，键盘录入用户名

​	2，键盘录入密码

​	3，键盘录入验证码

验证要求：

​		用户名如果未注册，直接结束方法，并提示：用户名未注册，请先注册

​		判断验证码是否正确，如不正确，重新输入

​		再判断用户名和密码是否正确，有3次机会

#### 忘记密码：

​       1，键盘录入用户名，判断当前用户名是否存在，如不存在，直接结束方法，并提示：未注册

​	2，键盘录入身份证号码和手机号码

​	3，判断当前用户的身份证号码和手机号码是否一致，

​			如果一致，则提示输入密码，进行修改。

​			如果不一致，则提示：账号信息不匹配，修改失败。

#### 验证码规则：

​	长度为5

​	由4位大写或者小写字母和1位数字组成，同一个字母可重复

​	数字可以出现在任意位置

比如：

​	aQa1K
