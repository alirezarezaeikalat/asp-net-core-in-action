0. (for mac users) first you have to run the image of sql server

        
1. to connect to the sql server: 

    npm install -g sql-cli
    mssql -u sa -p reallyStrongPwd123

2. on SQL server management studio you can determine the server name, if you want to connect to the database engine of a 
    remote machin you have to specify the ip address of the machine, if you want to connect to the database engine in your 
    local machine, you can use 127.0.0.1 or . or (local) in the server name field.
  
3. you can use create statement for creating all type of sql objects, including tables, dbs and ... :

    Create Database <db name>;

4. to rename a database:

      Alter Database Sample1 Modify Name = NewSample;

  we also can use a stored system procedure to rename a DB, stored system procedures can used for several tasks, and we use 
  one of them to change the name of the DB:

    sp_renameDB 'old name','new name';

5. We can use a Drop statement for deleting any sql objects:

      Drop database <db name>

6. If you want to drop a database, and another user is currently using this db, you will get an error, to resolve this, we 
    have to change the db to single user mode, and drop the db afterward:

      Alter Database <db name> Set SINGLE_USER With Roleback Immediate

7. Create table with create statement, you have to run this query in the query page of the specific db:
    
        CREATE TABLE tblGender
        (
            ID INT NOT NULL PRIMARY KEY,
            Gender NVARCHAR(50) NULL,
        )

8. If you are not in the specified db you can use this: 

        Use [db name]
        Go
        CREATE TABLE tblGender
        (
            ID INT NOT NULL PRIMARY KEY,
            Gender NVARCHAR(50) NULL,
        )

[Different constraint names]
foreign key / default / cascading referential integrity / check

9. Make a foreign key constraint for a table:
    
        ALTER TABLE tblPerson ADD CONSTRAINT FK_tblPerson_GenderId FOREIGN KEY
        (GenderId) REFERENCES tblGender (ID) 

10. To insert a record into a table: 

        Insert Into tblPersion (ID, Name, Email) Values (1, "alireza", "alireza@gmail.com")

11. To add default constraint for a field:

        Alter TABLE tblperson ADD CONSTRAINT DF_tblPerson_GenderId DEFAULT
        3 FOR GenderId 

12. Add new column to the table: 

        Alter Table <table name> Add <column name> <data type> <null | not null> CONSTRAINT <constraint name> DEFAULT <default value>

13. drop a constraint in a table:

        Alter Table <table name> Drop CONSTRAINT <constraint name>

14. cascading referential integrity constraint means that, if you delete a row in a table that is refference by another table, you
    will get an error, but there are some actions that can take place, if you delete a row in table that is referrenced by another table:
    (these rules applied to delete and update actions)
    
    No action: you will get an error because there are other records that refer to the the deleted to updated record

    Cascade: will delete or update all the record that refer to the deleted to updated record

    Set Null: set null all the records that refer to deleted or updated record

    Set default: set default all the record that refer to deleted or updated record 

15. Delete a row from a table: 

        Delete From <tbl name> Where <condition>

15. Check constraint limit the values of the variable in SQL server: 

    Alter Table <table> Add Constraint <name of the constraint> Check <bool expression>

    Alter table tblPersion Add Constraint CK-tblPersion_Age Check (Age > 0 AND Age < 150)
