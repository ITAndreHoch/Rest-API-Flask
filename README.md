# Project goal:

Automatic deployment of the environment to show HTTP-API (REST) that allows reading, writing, updating & deleting data from database.
Whole environment build automatically (just start by one command).
Our infrastructure consist of:
 - one virtual machine hostname: **node100** IP: **192.168.40.10**
 - three dockers (2 webs and one DB) Flas listen: **5000, 50001**, MYSQL: **3306**

# Description
 This build will automatically:
 - put one centos instance on VirtualBox
 - install necessery packages
 - create three dockers (one image from public repo - MYSQL, others from own image: flask+python)
 - perform parsing nad load data from titanic.csv into DB titanic - table titnic_tbl
 

Tech stack:
 - Virtual Box
 - Vagrant
 - Ansible
 - Mysql
 - Flask
 - Python modules
 
 
Remark: tested on the MacBookPro

# Prerequisites:

You must have installed:

1. Virtual Box
2. Vagrant tool made by *Hashi Corp*
3. Ansible


# Installation:


1. mkdir ./*your_path*/api; cd ./*your_path*/api 
2. git init
3. git clone https://gitlab.com/ahochbaum/API-Exercise.git
4. cd API-Exercise
5. vagrant up 

[Example installation log]  (https://gitlab.com/ahochbaum/API-Exercise/blob/master/files/installation.log)

# Chcecking:

**Dockers**
vagrant ssh node100
sudo su -

dockers:
```
# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
93c6366fd91e        ahochbaum/flask     "python main.py"         17 minutes ago      Up 17 minutes       0.0.0.0:5000->5000/tcp              flask1
4ae6db8d9cf3        ahochbaum/flask     "python main.py"         17 minutes ago      Up 17 minutes       0.0.0.0:5001->5000/tcp              flask2
865c49c71640        mysql:5.7           "docker-entrypoint..."   17 minutes ago      Up 17 minutes       0.0.0.0:3306->3306/tcp, 33060/tcp   mysqldb
```

**HTTP API**

API Get - you can check using browser - example:
http://192.168.40.10:5000/people/54
or
http://192.168.40.10:5001/people/54

Second way - using curl:
You can check all API METHODS using script:

```
# cat /root/mysql/api_test.sh 
echo "API GET ONE PERSON:"
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET http://localhost:5000/people/1
echo "-------------------"
#
echo "API DELETE ONE PERSON:"
curl -i -X DELETE http://192.168.40.10:5000/people/5
echo "-------------------"
#
echo "API POST:"
curl -d '{ "survived": true, "passengerClass": 3, "name": "Mr. Owen Harris Braund", "sex": "male", "age": 22, "siblingsOrSpousesAboard": 1, "parentsOrChildrenAboard":0, "fare":7.25}' -H "Content-Type: application/json" -X POST http://localhost:5000/people
echo "-------------------"
#
echo "API PUT:"
curl -d '{ "survived": true, "passengerClass": 3, "name": "Mr. Owen Harris Braund", "sex": "male", "age": 22, "siblingsOrSpousesAboard": 1, "parentsOrChildrenAboard":0, "fare":700.25}' -H "Content-Type: application/json" -X PUT http://localhost:5000/people/50
echo "-------------------"
#
echo "API GET ALL:"
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET http://localhost:5000/people
echo "-------------------"
#
```



**example:**

```

$ vagrant ssh node100
[vagrant@node100 ~]$ 
[vagrant@node100 ~]$ 
[vagrant@node100 ~]$ 
[vagrant@node100 ~]$ sudo su -
[root@node100 ~]# 
[root@node100 ~]# 
[root@node100 ~]# 
[root@node100 ~]# 
[root@node100 ~]# /root/mysql/api_test.sh 
API GET ONE PERSON:
HTTP/1.0 200 OK
Content-Type: application/json
Content-Length: 167
Server: Werkzeug/0.15.0 Python/2.7.16
Date: Wed, 20 Mar 2019 19:01:01 GMT

[{"age":22,"fare":7.25,"name":"Mr. Owen Harris Braund","parentsOrChildrenAboard":0,"passengerClass":3,"sex":"male","siblingsOrSpousesAboard":1,"survived":0,"uuid":1}]
-------------------
API DELETE ONE PERSON:
HTTP/1.0 200 OK
Content-Type: application/json
Content-Length: 29
Server: Werkzeug/0.15.0 Python/2.7.16
Date: Wed, 20 Mar 2019 19:01:01 GMT

"User deleted successfully!"
-------------------
API POST:
"User added successfully!"
-------------------
API PUT:
"User added successfully!"
-------------------
API GET ALL:
HTTP/1.0 200 OK
Content-Type: application/json
Content-Length: 152492
Server: Werkzeug/0.15.0 Python/2.7.16
Date: Wed, 20 Mar 2019 19:01:01 GMT

[{"age":22,"fare":7.25,"name":"Mr. Owen Harris Braund","parentsOrChildrenAboard":0,"passengerClass":3,"sex":"male","siblingsOrSpousesAboard":1,
"survived":0,"uuid":1},{"age":38,"fare":71.2833,"name":"Mrs. John Bradley (Florence Briggs Thayer) Cumings","parentsOrChildrenAboard":0,"passengerClass":1,
"sex":"female","siblingsOrSpousesAboard":1,"survived":1,"uuid":2},{"age":26,"fare":7.925,"name":"Miss. Laina Heikkinen","parentsOrChildrenAboard":0,
"passengerClass":3,"sex":"female","siblingsOrSpousesAboard":0,"survived":1,"uuid":3},{"age":35,"fare":53.1,"name":"Mrs. Jacques Heath (Lily May Peel) Futrelle",
"parentsOrChildrenAboard":0,"passengerClass":1,"sex":"female","siblingsOrSpousesAboard":1,"survived":1,"uuid":4},{"age":27,"fare":8.4583,"name":"Mr. James Moran",
"parentsOrChildrenAboard":0,"passengerClass":3,"sex":"male","siblingsOrSpousesAboard":0,"survived":0,"uuid":6},{"age":54,"fare":51.8625,"name":"Mr. Timothy J McCarthy","parentsOrChildrenAboard":0,"passengerClass":1,"sex":"male","siblingsOrSpousesAboard":0,"survived":0,"uuid":7},{"age":2,"fare":21.075,"name":"Master. Gosta Leonard Palsson","parentsOrChildrenAboard":1,"passengerClass":3,"sex":"male","siblingsOrSpousesAboard":3,"survived":0,"uuid":8},{"age":27,"fare":11.1333,"name":"Mrs. Oscar W (Elisabeth Vilhelmina 

[..]


```

# Files

**DOCKER**
docker-compose.yml:

```

version: '3'
 
services:
   web_master:
     image: ahochbaum/flask
     container_name: flask1
     ports:
      - "5000:5000"
   web_slave:
     image: ahochbaum/flask
     container_name: flask2
     ports:
      - "5001:5000"
   mysqldb:
     image: mysql:5.7
     container_name: mysqldb
     command: --default-authentication-plugin=mysql_native_password
     restart: always
     ports:
       - 3306:3306
     environment:
       MYSQL_ROOT_PASSWORD: root_password
       MYSQL_ROOT_HOST: 0.0.0.0
       MYSQL_DATABASE: titanic
       MYSQL_USER: titanic_user
       MYSQL_PASSWORD: titanic_password
     volumes:
        - /data/mysql:/var/lib/mysql:rw
        
```
**FLASK FILES**

REAMRK: MySQL Alchemy is finally recommended to connect and operate on MYSQL DB - Python

app.py:
```
from flask import Flask

app = Flask(__name__)

```

mysql_config.py:

```
from app import app
from flaskext.mysql import MySQL

mysql = MySQL()

# MySQL configurations
app.config['MYSQL_DATABASE_USER'] = 'titanic_user'
app.config['MYSQL_DATABASE_PASSWORD'] = 'titanic_password'
app.config['MYSQL_DATABASE_DB'] = 'titanic'
app.config['MYSQL_DATABASE_HOST'] = 'mysqldb'
mysql.init_app(app)

```

main.py:

```
import pymysql
from app import app
from mysql_config import mysql
from flask import jsonify,json
from flask import flash, request


# API GET all people from titanic_tbl
@app.route('/people')
def users():
		conn = mysql.connect()
		cursor = conn.cursor()
		cursor.execute('''SELECT * FROM titanic_tbl''')
		rows = cursor.fetchall()
                
                rows_d = []
                for tit in rows:
                    tit_d = {
                           'uuid': tit[0],
                           'survived': tit[1],
                           'passengerClass': tit[2],
                           'name': tit[3],
                           'sex': tit[4],
                           'age': tit[5],
                           'siblingsOrSpousesAboard': tit[6],
                           'parentsOrChildrenAboard': tit[7],
                           'fare': tit[8]}
                    rows_d.append(tit_d)
                return jsonify(rows_d)
                

# API GET one user from titanic_tbl
@app.route('/people/<uuid>', methods=['GET'])
def user(uuid):
		conn = mysql.connect()
		cursor = conn.cursor()
		cursor.execute("SELECT * FROM titanic_tbl WHERE uuid=%s", uuid)
		row = cursor.fetchall()

                row_d = []
                for tit in row:
                    tit_d = {
                           'uuid': tit[0],
                           'survived': tit[1],
                           'passengerClass': tit[2],
                           'name': tit[3],
                           'sex': tit[4],
                           'age': tit[5],
                           'siblingsOrSpousesAboard': tit[6],
                           'parentsOrChildrenAboard': tit[7],
                           'fare': tit[8]}
                    row_d.append(tit_d)
                return jsonify(row_d)


#API DELETE user from titanic_tbl
@app.route('/people/<uuid>', methods=['DELETE'])
def del_user(uuid):
        try:
                conn = mysql.connect()
                cursor = conn.cursor()
                cursor.execute("DELETE FROM titanic_tbl WHERE uuid=%s", uuid)
                conn.commit()
		resp = jsonify('User deleted successfully!')
		resp.status_code = 200
		return resp
	except Exception as e:
		print(e)
	finally:
		cursor.close() 
		conn.close()
		
# API POST add user into titanic_tbl
@app.route('/people', methods=['POST'])
def add_user():
                _json = request.json
                sur = _json['survived']
                _pass = _json['passengerClass']
                _name = _json['name']
                _sex = _json['sex']
                _age = _json['age']
                _sib = _json['siblingsOrSpousesAboard']
                _par = _json['parentsOrChildrenAboard']
                _fare = _json['fare']
                conn = mysql.connect()
                cursor = conn.cursor()
                cursor.execute('''INSERT INTO titanic_tbl \
                (survived, passengerClass, name, sex, age, siblingsOrSpousesAboard, parentsOrChildrenAboard, fare) \
                VALUES(%s, %s, %s, %s, %s, %s, %s, %s)''' ,(sur, _pass, _name, _sex, _age, _sib, _par, _fare))
                conn.commit()
                resp = jsonify('User added successfully!')
                resp.status_code = 200
                return resp

# API PUT update user in titanic_tbl
@app.route('/people/<uuid>', methods=['PUT'])
def update_user(uuid):
                _json = request.json
                sur = _json['survived']
                _pass = _json['passengerClass']
                _name = _json['name']
                _sex = _json['sex']
                _age = _json['age']
                _sib = _json['siblingsOrSpousesAboard']
                _par = _json['parentsOrChildrenAboard']
                _fare = _json['fare']
                conn = mysql.connect()
                cursor = conn.cursor()
                cursor.execute('''UPDATE titanic_tbl SET \
                survived=%s, passengerClass=%s, name=%s, sex=%s, age=%s, siblingsOrSpousesAboard=%s, parentsOrChildrenAboard=%s, fare=%s \
                WHERE uuid=%s''' ,(sur, _pass, _name, _sex, _age, _sib, _par, _fare, uuid))
                conn.commit()
                resp = jsonify('User added successfully!')
                resp.status_code = 200
                return resp


app.run(host='0.0.0.0', port= 5000)

```




