# Hive Installation With MySQL as Metastore

1. cd
2. wget < hive downloading link from apache website >
3. tar -xvf hive_downloaded.tar.gz
4. cp hive_downloaded /usr/local
5. ln -s hive_downloaded hive
6. gedit .bashrc
7. add this two lines in the .bashrc -> 
    - export HIVE_HOME=/usr/local/hive
    - export PATH=$PATH:$HIVE_HOME/bin
8. source .bashrc
9. hadoop fs -mkdir -p /usr/hive/warehouse
10. hadoop fs -mkdir /tmp
11. hadoop fs -chmod g+w /usr
12. hadoop fs -chmod g+w /usr/hive
13. hadoop fs -chmod g+w /usr/hive/warehouse
14. hadoop fs -chmod g+w /tmp
15. gedit /usr/local/hive/bin/hive-config.sh
16. add HADOOP_HOME full path ($HADOOP_HOME will not work) ->
    - export HADOOP_HOME=/usr/local/hadoop
17. cp /usr/local/hive/conf/hive-default.xml.template /usr/local/hive/conf/hive-site.xml
18. add few configurations here -> 
    1. these lines should be at the top ->
        ```    
        <property>
            <name>system:java.io.tmpdir</name>
            <value>/tmp/hive/java</value>
        </property>

        <property>
            <name>system:user.name</name>
            <value>${user.name}</value>
        </property>
        ``` 
    2. find these configurations and change the value according to yours ->
        ```
        <property>
            <name>javax.jdo.option.ConnectionURL</name>
            <value>jdbc:mysql://localhost/metastore?createDatabaseIfNotExist=true</value>
        </property>

        <property>
            <name>javax.jdo.option.ConnectionUserName</name>
            <value>your_username</value>
        </property>

        <property>
            <name>javax.jdo.option.ConnectionPassword</name>
            <value>your_password</value>
        </property>

        <property>
            <name>javax.jdo.option.ConnectionDriverName</name>
            <value>com.mysql.cj.jdbc.Driver</value>
        </property>
        ```
19. wget < download link of mysql_jdbc_connector >
20. cp mysql_connector.jar /usr/local/hive/lib/
21. rm /usr/local/hive/lib/guava-19.0.jar
22. cp /usr/local/hadoop/share/hadoop/hdfs/lib/guava-27.0-jre.jar /usr/local/hive/lib/
23. sudo apt-install mysql-server
24. sudo systemctl start mysql.service
25. sudo mysql
26. inside mysql prompt type ->
    - ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
    - exit;
27. sudo mysql_secure_installation
    - Now provide all the info required and the password
28. sudo mysql
29. inside mysql prompt type ->
    1. ALTER USER 'root'@'localhost' IDENTIFIED WITH auth_socket;
    2. CREATE USER 'your_username'@'localhost' IDENTIFIED BY 'your_password';
    3. GRANT ALL ON *.* TO 'your_username'@'localhost';
    4. FLUSH PRIVILEGES;
    5. exit;
30. mysql -u your_username -p
31. provide the password at 29.2 step and in mysql prompt type ->
    1. create database metastore;
    2. exit;
32. schematool -dbType mysql -initSchema
33. hive

Now the hive shell is opened. Type whatever hive command in the shell. And also you can create database with any other name rather than metastore. The datbase you are creating in the 31.i step, the same name you have to provide in the value section of the property javax.jdo.option.ConnectionURL in hive-site.xml. Value should be like this -> jdbc:mysql://localhost/your_database_name?createDatabaseIfNotExist=true
