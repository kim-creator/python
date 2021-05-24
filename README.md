# DB와 연동해서 파이썬에서 로그인 기능 구현하기

""" 추가 로그인 기능 """
# 제한횟수 3번 
# 회원가입 


import pymysql

conn = pymysql.connect(host='localhost', user = 'root', password='8924', db='login', charset='utf8')
cursor = conn.cursor()

# 로그인 실패 횟수
cnt = 0

try :
    while True:

        # ID, PW 입력받기
        id = input('ID 입력:')
        pw = input('PW 입력:')

        # DB에서 ID가 있는지 확인
        sql = "SELECT EXISTS (SELECT * FROM client WHERE id = %s) AS SUCCESS"
        cursor.execute(sql,(id))
        result = cursor.fetchone() # result는 tuple, result[0]는 int

        if result[0] == 1:

            # DB의 (ID, PWD)와 input값 비교
            sql = "SELECT id,pw FROM client WHERE id = %s"
            cursor.execute(sql,(id))
            result = cursor.fetchone() 
            if result[0] == id and result[1] == pw:
                print('\n로그인 성공!')
                break            
            else :
                cnt += 1
                print("******** 로그인 3회 실패 시 자동 종료됩니다 ********")
                print(f'로그인 {cnt}회 실패')
                print("****************************************************","\n")
            
            if cnt >= 3:
                print("보안을 위해 로그인 시스템을 종료합니다!")
                break

        else:
            print("\n##################################")
            print("ID가 존재하지 않습니다!!")
            print("##################################","\n")
            
            # Mini 회원가입 기능
            ans = input("아이디를 만드시겠습니까? [y/n]:")
            while True:
                if ans == 'y':
                    tmp1 = input("I D: >>>")
                    tmp2 = input("PWD: >>>")

                    # DB(ID,PWD)에 실수로 공백이 들어가는 것을 방지
                    if tmp1 == '':
                        print("\n만드실 ID를 입력하지 않았습니다.")
                        print("다시 입력해주세요!")
                        continue

                    if tmp2 == '':
                        print("\n만드실 Password를 입력하지 않았습니다.")
                        print("다시 입력해주세요!")
                        continue
                    
                    # DB에 넣어주기
                    sql = "INSERT INTO client(id,pw) VALUES(%s, %s)"
                    cursor.execute(sql,(tmp1, tmp2))
                    conn.commit()

                    # 제대로 들어갔는지 확인
                    sql = "SELECT * FROM client WHERE id = %s"
                    cursor.execute(sql,tmp1)
                    check_DB = cursor.fetchone()
                    print("######################################")
                    print(f"ID:{check_DB[1]}, PWD:{check_DB[2]}\n")
                    print("회원가입이 정상적으로 완료되었습니다!")
                    print("######################################\n")
                    break
                
                else:
                    print("\n시스템을 종료합니다!")
                    
                    # 이중 LOOP를 탈출하기 위해서 일부러 에러 만들기
                    raise NotImplementedError
                    

except NotImplementedError as e:
    print("----------------------------------")
    print("시스템 종료...")
    print("----------------------------------")

except Exception as e:
    print("############ 에러발생 ############")
    print(type(e))
    print(e)
    print("##################################")

finally:
    conn.close()


"""Reference"""
# # 기본 로그인 코드
# https://oceancoding.blogspot.com/2019/09/blog-post.html

# # 이중 Loop 탈출
# https://gomguard.tistory.com/190

# # Delete Multiple rows
# https://www.tutorialspoint.com/How-can-we-delete-multiple-rows-from-a-MySQL-table
show databases;

create DATABASE login;

use login;

show tables;

desc client;


select * from client;

-- 새로운 테이블 생성 --
CREATE TABLE client(pnum int primary key auto_increment, id char(10), pw char(10));

-- clent 테이블의 id 필드에 primary key 속성 부여 --
 alter table client drop primary key(id);

-- PK 속성을 없애기 --
 alter table client drop primary key;


-- 컬럼 순서 바꾸기(다른컬럼 다음으로)--
ALTER TABLE 테이블명 MODIFY COLUMN 컬럼명 자료형 AFTER 다른컬럼;
-- 컬럼 순서 바꾸기(원하는 것을 제일 처음으로)--
ALTER TABLE client MODIFY COLUMN pnum int FIRST;
-- 클라이언트 테이블에서 pk 삭제 --
alter table client drop primary key;
-- 원하는 컬럼에 pk속성을 부여 --
alter TABLE client add(pnum int primary key auto_increment);

insert into client(id, pw) values('oh1234','1234oh');
insert into client(id, pw) values('woo1234','1234woo');
insert into client(id, pw) values('kim1234','1234kim');
insert into client(id, pw) values('choi1234','1234choi');
insert into client(id, pw) values('park1234','1234park');

alter table client modify id varchar(50);
alter table client modify pw varchar(50);

drop table clinet;
#파이썬 스터디 2

*************************************
# id,pw 필드를 null에서 not null로 바꿔줌
*************************************


mysql> ALTER TABLE client MODIFY COLUMN id char(10) not null;
mysql> ALTER TABLE client MODIFY COLUMN pw char(10) not null;

*** 기존 ***
mysql> desc client;
+-------+----------+------+-----+---------+-------+
| Field | Type     | Null | Key | Default | Extra |
+-------+----------+------+-----+---------+-------+
| id    | char(10) | YES  |     | NULL    |       |
| pw    | char(10) | YES  |     | NULL    |       |
+-------+----------+------+-----+---------+-------+

*** 변경후 ***

mysql> desc client;
+-------+----------+------+-----+---------+-------+
| Field | Type     | Null | Key | Default | Extra |
+-------+----------+------+-----+---------+-------+
| id    | char(10) | NO   |     | NULL    |       |
| pw    | char(10) | NO   |     | NULL    |       |
+-------+----------+------+-----+---------+-------+



*************************************
# id가 PRIMARY KEY 값을 갖도록 함
*************************************


client 라는 테이블의 id라는 필드에
primary key 속성을 부여

mysql> alter table client
    -> add primary key(id);

mysql> desc client;
+-------+----------+------+-----+---------+-------+
| Field | Type     | Null | Key | Default | Extra |
+-------+----------+------+-----+---------+-------+
| id    | char(10) | NO   | PRI | NULL    |       |
| pw    | char(10) | NO   |     | NULL    |       |
+-------+----------+------+-----+---------+-------+


*************************************
# 회원 정보 입력
*************************************
https://www.simplifiedpython.net/python-gui-login/


***********************
완성된 코드
***********************

#import modules

from tkinter import *
import os

# Designing window for registration

def register():
    global register_screen
    register_screen = Toplevel(main_screen)
    register_screen.title("Register")
    register_screen.geometry("300x250")

    global username
    global password
    global username_entry
    global password_entry
    username = StringVar()
    password = StringVar()

    Label(register_screen, text="Please enter details below", bg="blue").pack()
    Label(register_screen, text="").pack()
    username_lable = Label(register_screen, text="Username * ")
    username_lable.pack()
    username_entry = Entry(register_screen, textvariable=username)
    username_entry.pack()
    password_lable = Label(register_screen, text="Password * ")
    password_lable.pack()
    password_entry = Entry(register_screen, textvariable=password, show='*')
    password_entry.pack()
    Label(register_screen, text="").pack()
    Button(register_screen, text="Register", width=10, height=1, bg="blue", command = register_user).pack()


# Designing window for login 

def login():
    global login_screen
    login_screen = Toplevel(main_screen)
    login_screen.title("Login")
    login_screen.geometry("300x250")
    Label(login_screen, text="Please enter details below to login").pack()
    Label(login_screen, text="").pack()

    global username_verify
    global password_verify

    username_verify = StringVar()
    password_verify = StringVar()

    global username_login_entry
    global password_login_entry

    Label(login_screen, text="Username * ").pack()
    username_login_entry = Entry(login_screen, textvariable=username_verify)
    username_login_entry.pack()
    Label(login_screen, text="").pack()
    Label(login_screen, text="Password * ").pack()
    password_login_entry = Entry(login_screen, textvariable=password_verify, show= '*')
    password_login_entry.pack()
    Label(login_screen, text="").pack()
    Button(login_screen, text="Login", width=10, height=1, command = login_verify).pack()

# Implementing event on register button

def register_user():

    username_info = username.get()
    password_info = password.get()

    file = open(username_info, "w")
    file.write(username_info + "\n")
    file.write(password_info)
    file.close()

    username_entry.delete(0, END)
    password_entry.delete(0, END)

    Label(register_screen, text="Registration Success", fg="green", font=("calibri", 11)).pack()

# Implementing event on login button 

def login_verify():
    username1 = username_verify.get()
    password1 = password_verify.get()
    username_login_entry.delete(0, END)
    password_login_entry.delete(0, END)

    list_of_files = os.listdir()
    if username1 in list_of_files:
        file1 = open(username1, "r")
        verify = file1.read().splitlines()
        if password1 in verify:
            login_sucess()

        else:
            password_not_recognised()

    else:
        user_not_found()

# Designing popup for login success

def login_sucess():
    global login_success_screen
    login_success_screen = Toplevel(login_screen)
    login_success_screen.title("Success")
    login_success_screen.geometry("150x100")
    Label(login_success_screen, text="Login Success").pack()
    Button(login_success_screen, text="OK", command=delete_login_success).pack()

# Designing popup for login invalid password

def password_not_recognised():
    global password_not_recog_screen
    password_not_recog_screen = Toplevel(login_screen)
    password_not_recog_screen.title("Success")
    password_not_recog_screen.geometry("150x100")
    Label(password_not_recog_screen, text="Invalid Password ").pack()
    Button(password_not_recog_screen, text="OK", command=delete_password_not_recognised).pack()

# Designing popup for user not found
 
def user_not_found():
    global user_not_found_screen
    user_not_found_screen = Toplevel(login_screen)
    user_not_found_screen.title("Success")
    user_not_found_screen.geometry("150x100")
    Label(user_not_found_screen, text="User Not Found").pack()
    Button(user_not_found_screen, text="OK", command=delete_user_not_found_screen).pack()

# Deleting popups

def delete_login_success():
    login_success_screen.destroy()


def delete_password_not_recognised():
    password_not_recog_screen.destroy()


def delete_user_not_found_screen():
    user_not_found_screen.destroy()


# Designing Main(first) window

def main_account_screen():
    global main_screen
    main_screen = Tk()
    main_screen.geometry("300x250")
    main_screen.title("Account Login")
    Label(text="Select Your Choice", bg="blue", width="300", height="2", font=("Calibri", 13)).pack()
    Label(text="").pack()
    Button(text="Login", height="2", width="30", command = login).pack()
    Label(text="").pack()
    Button(text="Register", height="2", width="30", command=register).pack()

    main_screen.mainloop()


main_account_screen()
# 장고 로그인 구현 쉽게 할수 있는 곳
https://docs.djangoproject.com/en/3.2/ref/contrib/auth/

# 장고 로그인 완성 소스(깃헙)
https://github.com/pahkey/djangobook/tree/3-05

#소셜로그인 구현하기
https://hazel-developer.tistory.com/79

#OAuth 완벽하게 이해하기
https://velog.io/@parkoon/OAuth-%EC%99%84%EB%B2%BD%ED%95%98%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0

# 파이썬 예제(로그인 시스템)
https://oceancoding.blogspot.com/2019/09/blog-post.html
