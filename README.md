# Java-ATM-system
ATM系统
# ATM系统功能实战

## 系统准备、首页设计

**系统准备内容分析：**

①每个用户的账户信息都是一个对象，需要提供账户类。

②需要准备一个容器，用于存储系统全部账户对象信息。

③首页只需要包含：登录和注册2个功能。

**实现步骤：**

①定义账户类，用于后期创建账户对象封装用户的账户信息。

②账户类中的信息至少需要包含（卡号、姓名、密码、余额、取现额度）

③需要准备一个ArrayList的集合，用于存储系统用户的账户对象。

④定义一个系统启动类ATMSystem需要展示欢迎页包含2个功能：开户功能、登录账户。

```java
package ATM;

public class Account {

    /**
     系统的账户类，代表账户的信息
     */
        private String cardId; // 卡号
        private String userName; // 用户名称
        private String passWord; // 密码
        private double money; // 账户余额
        private double quotaMoney; // 每次取现额度限度。

        public String getCardId() {
            return cardId;
        }

        public void setCardId(String cardId) {
            this.cardId = cardId;
        }

        public String getUserName() {
            return userName;
        }

        public void setUserName(String userName) {
            this.userName = userName;
        }

        public String getPassWord() {
            return passWord;
        }

        public void setPassWord(String passWord) {
            this.passWord = passWord;
        }

        public double getMoney() {
            return money;
        }

        public void setMoney(double money) {
            this.money = money;
        }

        public double getQuotaMoney() {
            return quotaMoney;
        }

        public void setQuotaMoney(double quotaMoney) {
            this.quotaMoney = quotaMoney;
        }
    }
```

```java
package ATM;

import java.sql.SQLOutput;
import java.util.ArrayList;
import java.util.Random;
import java.util.Scanner;
import java.util.ServiceConfigurationError;

public class ATMSysteam {
    public static void main(String[] args) {
                // 2、创建一个集合对象，用于后期存入账户对象。
                ArrayList<Account> accounts = new ArrayList<>();

                // 3、展示系统欢迎页面
                System.out.println("======欢迎您进入到ATM系统===============");
                while (true) {
                    System.out.println("1、登录账户");
                    System.out.println("2、注册账户");
                    System.out.println("请您选择操作：");
                    Scanner sc = new Scanner(System.in);
                    int command = sc.nextInt();
                    switch (command) {
                        case 1:
                            // 登录操作
                            login(accounts, sc);
                            break;
                        case 2:
                            // 注册账户
                            register(accounts, sc);
                            break;
                        default:
                            System.out.println("当前输入的操作不存在！");
                    }
                }
            }
```

用户的账户信息，系统如何表示的？ ⚫ 定义账户类Account，定义系统关心的属性信息。 2、系统采用什么来存储全部用户的账户对象信息？ ArrayList accounts = new ArrayList<>()；



## 用户开户功能实现

开户功能其实就是就是往系统的集合容器中存入一个新的账户对象的信息。

```java
/**
 * 开户功能
 */
private static void register(ArrayList<Account> accounts, Scanner sc) {
    System.out.println("==================欢迎您进入到开户操作======================");
    // 2、创建一个账户对象封装账户信息
    Account acc = new Account();
    // 1、录入用户账户信息
    System.out.println("请您输入账户名称：");
    String userName = sc.next();
    acc.setUserName(userName);

    while (true) {
        System.out.println("请您输入账户密码：");
        String passWord = sc.next();
        System.out.println("请您输入确认密码：");
        String okPassWord = sc.next();
        if (okPassWord.equals(passWord)) {
            // 密码无问题
            acc.setPassWord(okPassWord);
            break;
        } else {
            System.out.println("两次输入的密码不一致！");
        }
    }
    System.out.println("请您设置当次取现额度：");
    double quataMoney = sc.nextDouble();
    acc.setQuotaMoney(quataMoney);
    // 关键点：为当前账户生成一个随机的8位数字作为卡号，卡号不能与其他用户的卡号重复。
    String cardId = createCardId(accounts);
    acc.setCardId(cardId);

    // 3、把账户对象存入到集合容器对象中去
    accounts.add(acc);
    System.out.println("恭喜您," + acc.getUserName() + "先生/女士，您开户完成，您的卡号是：" + acc.getCardId());
}
```

**开户功能实现步骤**

①定义方法完成开户：

②键盘录入姓名、密码、确认密码（需保证两次密码一致）

③生成账户卡号，卡号必须由系统自动生成8位数字（必须保证卡号的唯一）

④创建Account账户类对象用于封装账户信息（姓名、密码、卡号）

⑤把Account账户类对象存入到集合accounts中去。

```java
 
```

## 用户登录功能实现

**分析**

①定义方法：

②让用户键盘录入卡号，根据卡号查询账户对象。

③如果没有找到了账户对象，说明卡号不存在，提示继续输入卡号。

④如果找到了账户对象，说明卡号存在，继续输入密码。

⑤如果密码不正确，提示继续输入密码

⑥如果密码正确，提示登陆成功！！

```java
   /**
             * 用户登录功能
             */
            private static void login(ArrayList<Account> accounts, Scanner sc) {
                System.out.println("==================欢迎您进入到登录操作======================");
                if (accounts.size() > 0) {
                    while (true) {
                        System.out.println("请您输入登录的卡号：");
                        String cardId = sc.next();
                        // 2、根据卡号去集合中查询是否存在账户对象
                        Account acc = getAccountByCardId(cardId, accounts);
                        // 判断账户对象是否存在，存在说明卡号正确
                        if (acc != null) {
                            while (true) {
                                // 3、判断密码是否正确
                                System.out.println("请您输入登录的密码：");
                                String passWord = sc.next();
                                if (acc.getPassWord().equals(passWord)) {
                                    // 登录成功了！
                                    System.out.println("欢迎你：" + acc.getUserName() + "先生/女士进入系统，您可开始办理你的业务了!");
                                    // 展示登录成功后的操作界面。
                                    showCommand(sc, acc, accounts);
                                    return;
                                } else {
                                    System.out.println("您的密码不正确！");
                                }
                            }
                        } else {
                            System.out.println("卡号不存在，请确认！");
                        }
                    }
                } else {
                    System.out.println("当前系统无任何账户，请先注册再登录！");
                }
            }

```

## 用户操作页设计、查询账户、退出账户功能实现





①用户登录成功后，需要进入用户操作页，退出账户是需要回到首页的。

```java
 /**
             * 登录后展示的界面
             */
            private static void showCommand(Scanner sc, Account acc, ArrayList<Account> accounts) {
                while (true) {
                    System.out.println("==================欢迎您进入到操作界面======================");
                    System.out.println("1、查询");
                    System.out.println("2、存款");
                    System.out.println("3、取款");
                    System.out.println("4、转账");
                    System.out.println("5、修改密码");
                    System.out.println("6、退出");
                    System.out.println("7、注销账户");
                    System.out.println("请您输入操作命令：");
                    int command = sc.nextInt();
                    switch (command) {
                        case 1:
                            // 查询账户信息展示
                            showAccount(acc);
                            break;
                        case 2:
                            // 存款
                            depositMoney(acc, sc);
                            break;
                        case 3:
                            // 取款
                            drawMoney(acc, sc);
                            break;
                        case 4:
                            // 转账
                            transferMoney(acc, accounts, sc);
                            break;
                        case 5:
                            // 修改密码
                            updatePassWord(acc, sc);
                            return; // 跳出当前操作的方法，最终回到首页
                        case 6:
                            // 退出 回到首页
                            System.out.println("欢迎下次继续光临！！");
                            return; // 结束登录后的全部操作
                        case 7:
                            // 注销账户
                            accounts.remove(acc); // 从集合对象中删除当前账户对象。
                            System.out.println("您的账户已经完成了销毁，您将不可以进行登录了！");
                            return;
                        default:
                            System.out.println("您的操作命令有误！");
                    }
                }

            }

```

②查询就是直接展示当前登录成功的账户对象的信息。

## 用户存款功能实现

```
private static void showAccount(Account acc) {
    System.out.println("==================您当前账户详情信息如下======================");
    System.out.println("卡号：" + acc.getCardId());
    System.out.println("户主：" + acc.getUserName());
    System.out.println("余额：" + acc.getMoney());
    System.out.println("当次取现额度：" + acc.getQuotaMoney());
}
```

①存款就是拿到当前账户对象。

②然后让用户输入存款的金额。

③调用账户对象的setMoney方法将账户余额修改成存钱后的余额。

④存钱后需要查询一下账户信息，确认是否存钱成功了！

```java
  private static void depositMoney(Account acc, Scanner sc) {
                System.out.println("==================欢迎进入账户存款操作======================");
                System.out.println("请您输入存款金额：");
                double money = sc.nextDouble();
                acc.setMoney(acc.getMoney() + money);
                showAccount(acc);
            }
```

## 用户取款功能实现



**取款分析**

①取款需要先判断账户是否有钱。

②有钱则拿到自己账户对象。

③然后让用户输入取款金额

④判断取款金额是否超过了当次限额，以及余额是否足够

⑤满足要求则调用账户对象的setMoney方法完成金额的修改。

```java
      
```

## 用户转账功能实现



**分析**

①转账功能需要判断系统中是否有2个账户对象及以上。

②同时还要判断自己账户是否有钱。

③接下来需要输入对方卡号，判断对方账户是否存在。

④对方账户存在还需要认证对方户主的姓氏。

⑤满足要求则可以把自己账户对象的金额修改到对方账户对象中去。

```java
  /**
             * 从当前账户对象中把金额转给其他账户对象。
             *
             * @param acc
             * @param accounts
             * @param sc
             */
            private static void transferMoney(Account acc, ArrayList<Account> accounts, Scanner sc) {
                // 1、判断自己的账户中是否有钱
                if (acc.getMoney() <= 0) {
                    System.out.println("您自己都没钱，就别转了吧！");
                    return;
                }

                // 2、判断总账户数量是否大于等于2个。
                if (accounts.size() >= 2) {
                    while (true) {
                        // 3、让当前用户输入对方的账号进行转账
                        System.out.println("请您输入对方卡号：");
                        String cardId = sc.next();

                        // 4、根据卡号查询出集合中的账户对象
                        Account otherAcc = getAccountByCardId(cardId, accounts);
                        // 5、判断账户对象是否存在，而且这个账户对象不能是自己。
                        if (otherAcc != null) {
                            // 6、判断当前账户是否是自己。
                            if (acc.getCardId().equals(otherAcc.getCardId())) {
                                System.out.println("不能给自己账户转账！");
                            } else {
                                // 7、正式进入到转账逻辑了
                                // 黑马刘德华
                                String rs = "*" + otherAcc.getUserName().substring(1);
                                System.out.println("请您确认[" + rs + "]的姓氏来确认！");
                                System.out.println("请您输入对方的姓氏：");
                                String preName = sc.next();
                                if (otherAcc.getUserName().startsWith(preName)) {
                                    // 认证通过
                                    while (true) {
                                        System.out.println("请您输入转账的金额（您最多可以转账：" + acc.getMoney() + "元）：");
                                        double money = sc.nextDouble();
                                        if (money > acc.getMoney()) {
                                            System.out.println("你不听话，没有这么多钱可以转！");
                                        } else {
                                            // 开始转
                                            acc.setMoney(acc.getMoney() - money); // 更新自己账户
                                            otherAcc.setMoney(otherAcc.getMoney() + money);
                                            System.out.println("您已经完成转账！您当前还剩余：" + acc.getMoney());
                                            return;
                                        }
                                    }

                                } else {
                                    System.out.println("您输入对方的信息有误！");
                                }
                            }
                        } else {
                            System.out.println("您输入的转账卡号不存在！");
                        }
                    }
                } else {
                    System.out.println("当前系统中没有其他账户可以转账，去注册一个账户吧！");
                }

            }
```

## 用户密码修改、销户功能实现



**分析**

①修改密码就是把当前对象的密码属性使用set方法进行更新。

```java
 /**
             * 修改当前账户对象的密码
             *
             * @param acc
             */
            private static void updatePassWord(Account acc, Scanner sc) {
                // 1、判断旧密码是否正确
                while (true) {
                    System.out.println("请您输入当前密码认证：");
                    String passWord = sc.next();
                    if (acc.getPassWord().equals(passWord)) {
                        while (true) {
                            // 2、输入新密码
                            System.out.println("请您输入新密码：");
                            String newPassWord = sc.next();
                            System.out.println("请您确认新密码：");
                            String okPassWord = sc.next();
                            // 3、比对两次密码是否一致
                            if (newPassWord.equals(okPassWord)) {
                                acc.setPassWord(okPassWord);
                                System.out.println("密码已经修改成功，请重新登录！");
                                return;
                            } else {
                                System.out.println("两次密码不一致！");
                            }
                        }
                    } else {
                        System.out.println("您输入的密码有误。请重新确认密码！");
                    }
                }
            }

```

②销户是从集合对象中删除当前对象，并回到首页。
