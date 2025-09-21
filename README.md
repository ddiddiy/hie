```bash
PRACTICAL - 1 (Working with hdfs)
java -version
hdfs namenode -format
start.all-cmd

Hadoop fs -mkdir /shelly
Hadoop fs -mkdir /input

Hadoop fs -ls /input

"create a txt file"
Hadoop fs -put c:\user\demo.txt /shelly
Hadoop fs -ls /shelly
Hadoop fs -get /shelly/demo.txt c:\user\demo.txt
Hadoop fs -cp /shelly/demo.txt /input

Hadoop fs -mkdir /second
Hadoop fs -mv /shelly/demo.txt /second
Hadoop fs -ls /second

Hadoop fs -rmdir /input   (deletes only empty folders)
Hadoop fs -rm -r /second  (deleted non- empty folders)


PRACTICAL - 1C  (MapReduce)
create a new folder on desktop(pract)
copy paste code given on GC 
save as wordcount.java
open cmd on that path

download that zip file given on gc
create WC in 'shelly' folder
javac -classpath hadoop-core-1.2.1.jar -d WC wordcount.java
jar -cvf wordcount.jar -C WC/.
Hadoop fs -put c:pract\demo.txt /shelly
Hadoop fs -cat c:pract\demo.txt /shelly

Hadoop jar c:\pract\wordcount.jar wordcount /shelly /outputdir
Hadoop fs -ls /outputdir
Hadoop fs -cat /outputdir/part-r-00000

PRACTICAL - 2   (HIVE & DERBY)
Hadoop namenode -format
start.all-cmd
startNetworkServer -h 0.0.0.0
hive --service schematool -dbType derby -initSchema
hive
hive> show databases;
create database if not exists tyit;
create databases demo1 WITH DBPROPERTIES('creator' = 'shelly', 'date' = '27-07-2025');
drop database demo1;
create table employee(ID int, Name string, salary float)row format delimited fields terminated by ',';
describe employee;
insert into employee values(1, 'shelly', 1000000);
select * from employee;

c:> create a new file student.csv
Hadoop fs -makdir /hivefolder
Hadoop fs -put c:\desktop\hive&derby\student.csv /hivefolder
Hadoop fs -ls /hivefolder
Hadoop fs -cat /hivefoler/student.csv

hive> create external table emp1(id int, name string, salary float)row format delimited fields terminated by ',' location '/hivefolder';
select * emp1;


PRACTICAL - 3 (PIG)
Create a folder "data" in c drive 
create a subfolder "db" in data
c> mongod
mongo sh
show dbs;
pig

c> hdfs namenode -format
start-all.cmd
pig
create 'std.csv' in folder 'shelly' on desktop
Hadoop fs -mkdir /pig
Hadoop fs -ls /pig
Hadoop fs -put c:\user\shelly\std.csv /pig
Hadoop fs -ls /pig
Hadoop fs -cat /pig/std.csv

c> pig
grunt> A = LOAD '/pig/std.csv' USING PigStorage(',') AS (Roll_no: int,name chararray, percentage: float, class: chararray);
DUMP A;
grpa = GROUP A BY Class;
DUMP grpa

cnt1 = FOREACH grpa GENERATE COUNT($1);
DUMP cnt1
cnt1 = FOREACH grpa GENERATE CONCAT((chararray)$0,CONCAT(':',(chararray)COUNT($1)));
DUMP cnt1

cnt2 = FOREACH grpa GENERATE CONCAT((chararray)$0, CONCAT(':',(chararray)AVG($1.$2)));
DUMP cnt2
alldata = GROUP A ALL;
avgall = FOREACH alldata GENERATE AVG(A.$2);
DUMP avgall

PRACTICAL - 4   (CRUD IN MONGODB)
mongosh
show dbs;
use Bhadra;
drop db Bhadra;
use tyit;
db.createCollection('student');
show collections;
db.student.drop()

#to insert one value
db.student.insert({name: 'Gada',age: 20, percentage: 89, subject:['MAD','AI','BD','DV']});

#to insert many values
db.student.insert_many([{name: 'Gada',age: 20, percentage: 89, subject:['MAD','AI','BD','DV']},{name: 'Parv',age: 15,percentage: 90, subjects:['MAD','AI','maths']}]);

#to show records
db.student.find();

#to delete one value
db.student.deleteOne({name: 'gada'});

#to update a record
db.student.update({name: 'parv'}, {$set: {percentage: 85}});

#to update multiple record
db.student.update({name: 'parv'}, {$set: {percentage: 80}});

#to update one
db.student.updateOne({age: {$gt: 21}},{$set: {age: 25}});

#to find a record
db.student.find({name: {$eq: 'shelly'}});

#to match any value in a specified array
db.student.find({student: {$in: ['dv','ai']}});

#not in the array
db.student.find({student: {$nin: ['bd']}});

db.student.find({$and: [{age: 30},{subjects:[{$in: ['dv','ai']}]}]});
db.student.find({$or: [{age: 25},{subject: {$in: ['dv']}}]});
db.student.find({percentage: {$exists: true}});
db.student.find({uid: {$exists: false}});
db.student.find().limit(2);

#1 is the "name" field
db.student.find({}, {name:1});

#0 id the "other" fields
db.student.find({}, {name:0});

db.student.find({percentage: {$gt: 75}},{name: 1});

#sorts percentage in ascending order
db.student.find().sort({percentage: 1});

#sorts in descending order
db.student.find().sort({percentage: -1});

db.student.find().sort({percentange: -1, age: 1});

PRACTICAL - 6   (Perform aggregation operation and sorting)
db.employee.aggregate([{$project: {emp_name: 1, salary: 1, _id: 0}}]);

db.employee.aggregate([{$match: {salary: {$gt: 700000}}}])

db.employee.count();

db.employee.aggregate([{$group:{_id:'$dept:', salary:{$sum: '$salary'}}}}])

db.employee.aggregate([{$group:{_id:'$dept:', avgsalary:{$avg: '$salary'}}}])

db.employee.aggregate([{$group: {_id: null, maxsalary: {$max: '$salary'}}}])

db.employee.aggregate([{$match: {desg: 'IT head'}},{$group: {_id: null, maxsalary: {$max: 'salary'}}}])

db.employee.aggregate([{$match: {dept: {$nin: ['HR','Finance']}}},{$group:{_id:null, totalsalary: {$sum:'$salary'}}}]);

db.employee.aggregate([{$sort: {dept:1}}, {$group: {_id:null, firstsal: {$first: '$salary'}}}]);

db.employee.aggregate([{$limit: 3}, {$group: {_id: null, totalsalary{$sum: '$salary'}}}])


PRACTICAL - 5 (Indexes)
db.employee.find({emp_name: 'shelly'}).explain('executionStats');

db.employee.createIndex({emp_name:1});
db.employee.getIndexes();

db.employee.createIndex({emp_name:1,dept:1})
db.employee.dropIndex({name_1_dept_1})

db.employee.find({emp_name: 'shelly', dept: 'IT'}).explain('executionstats')

db.employee.dropIndex('name_1')


PRACTICAL - 7  (python script for CRUD)

import pymongo
mycli = pymongo.MongoClient('mongodb://localhost:27017/')
mydb = mycli['tyit']
mycol = mydb['employee']
mydoc = mycol.find()

for i in mydoc:
print(i)


#all doc with salary > 90000
import pymongo 
mycli = pymongo.MongoClient('mongodb://27017/')
mydb = mycli['tyit']
mydoc = mycol.find({salary: {$gte: 90000}})

for i in doc:
print(i)


#take user input for salary
import pymongo
mycli = pymongo.MongoClient('mongodb://27017/')
mydb = mycli['tyit']
mycol = mydb['employee']
sal = int(intput('Enter the salary'))
mydoc = mycol.find({'salary': {$gt: sal}})

for i in mydoc:
print(i)


#finding emp by name using user input
import pymongo 
mycli = pymongo.MongoClient('mongodb://27017/')
mydb = mycli['tyit']
mycol = mydb['employee']
name = str(input('enter name: '))
mydoc = mycol.find({'emp_name': name}) 

for i in mydoc:
print(i)


#user input to inser new employee
import pymongo
mycli = pymongo.MongoClient('mongodb://27017/')
mydb = mycli['tyit']
mycol = mydb['employee']
emp_id = int(input('Enter emp id: '))
name = str(input('Enter name: '))
email = str(input('enter email: '))
salary = int(input('enter salary: '))
dept = str(input("Enter Dept: "))
desg = str(input("Enter Designation: "))

mycol.insert_one({'emp_id': emp_id, 'emp_name':name, 'emp_email':email, 'salary':salary,'dept': dept, 'desg':desg})


#insert many
import pymongo
mycli = pymongo.MongoClient('mongodb://27017')
mydb = mycli['tyit']
mycol = mydb['employee']

employee = [
	{
		'emp_id': 2010,
		'emp_name': 'shelly'
		'email': 'shellyjain0045#Gmail.com',
		'salary': 900000,
		'designation': IT head,
		'dept': IT
	}

	{
		'emp_id': 2010,
		'emp_name': 'shelly'
		'email': 'shellyjain0045#Gmail.com',
		'salary': 900000,
		'designation': IT head,
		'dept': IT
	}
]

mycol.insert_many(employees)


#update one
import pymongo
mycli = mongodb.MongoClient['mongodb://27017']
mydb = mycli['tyit']
mycol = mydb['employee']

print(mycol.updte_many({'dept': 'IT'},{'$set': {'salary':}}).modified_count)


#delete many:
import pymongo
mycli = pymongo.MongoClient('mongodb://27017')
mydb = mycli['tyit']
mycol = mydb['employee']

print(mycol.delete_many({'desg': 'member'}).delete_count, 'document deleted')


PRACTICAL - 8  (python script to perform filter, sort and aggregation query in mongoDB)

#Ask user either he wants to do asec or desc:
import pymongo

mycli = MyClient('mongodb://27017')
mydb = mycli['tyit']
mycol = mydb['employee']
user = int(input('Enter ur choice 1 or 2: '))

if user == 1:
doc = mycol.find().sort('emp_name',1)
elif user == 2:
doc = mycol.find().sort('emp_name',-1)

for i in doc:
print(i)


#Show avg salary dept wise using python
import pymongo
mycli = pymongo MongoClient('mongodb:27017')
mydb = mycli['tyit']
mycol = mydb['employee']

res = mycol.aggregate([
	{
		'$group': {
			'_id':'$dept',
			'avd_salary': {'$avg': '$salary'}		
		}
	}
])

for i in res:
	print(i)


#With user input with specific dept
import pymongo
mycli = pymongo.MongoClient['mongodb://27017']
mydb =mycli['tyit']
mycol = mydb['employee']

res = mycol.aggregate([
{
'$match':{
	'dept': dept
}
},
{
$group:{
'_id':"$dept",
'avg_salary':{'$avg':'$salary'}
}
}
])

for i in res:
print(i)


#only show avg salary of greater than 70000
import pymongo
mycli = pymongo.MongoClient('mongodb://27017')
mydb = mycli['tyit']
mycol = mydb['employee']

avg_sal = mycol.aggregate([
{
'$match':{
	'salary':{'$gt': 70000}
}
},
{
'$group':{
	'_id':'$dept',
	'avg_sal':{'$avg':'$salary'}
}
}
])

for i in avg_sal:
print(i)


PRACTICAL - 9 (Flask application with pymongo)

refer vs code (flask folder)
```
