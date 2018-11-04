---
title: ADO.NET与外部数据库连接及查询等基本操作
date: 2017-05-21 15:37:04
tags: [.net,c#]
categories: 技术
comments: true 
---
## 建立与数据库连接: ##

**需要连接字符串，用到System.Data.SqlClient类库。**

方法：

 - 使用一个类构建SQL Server 连接字符串
       -  创建SqlconnectionStringBuider实例
       - 根据需要设置其属性
       - 访问该对象的ConnectionString属性
 - 与SQL Server数据库建立连接
       - 生成一个指向该数据库的连接字符串
       - 创建SqlConnection实例，向构造函数传递连接字符串
       - 调用Sqlconnection实例的open方法
   ##    代码实例：
		   
			   /*建立连接字符串生成器*/
			SqlConnectionStringBuilder connection = new SqlConnectionStringBuilder();
     
			 /*能连上本地服务器，以windows身份登录*/
		    if (LocalServer.Checked == true)
                      connection.DataSource = "(local)";
              
			         /*以sql身份登录*/
	         else
                       connection.DataSource = ServerName.Text;

		        /*若sql server是express版本*/
	         if (IsExpressEdition.Checked == true)
                       connection.DataSource += @"\SQLEXPRESS";
             
		        /*基于当前windows登陆的集成安全验证*/
	         if(AuthenticateWindows.Checked == true)
	         {
	              connection.IntegratedSecurity = true;
	         }                       
	         else
	         {
	             connection.IntegratedSecurity = false;//基于SQL用户的安全验证
	             connection.UserID = UserName.Text;
	             connection.Password = UserPassword.Text;
	         }
				 SqlConnection linkToDB = new SqlConnection(connection.ConnectionString);//新建连接对象
				 linkToDB.Open();//打开数据库连接
					 linkToDB.close();//关闭数据库，或用dispose方法，或者用using语句就会自动关闭
## 对数据库进行查询、修改、更新、删除等操作：##
**需要用到System.Data.SqlClient类库中的sqlcommand类**

方法：

 - 通过一个ADO.NET连接运行SQL查询
       -  创建Sqlcommandr实例
       - 将其CommandText属性设置为SQL语句
       - 将其Connection属性设置为一个有效的Sqlconnection实例
       - 调用这个命令对象的ExecuteNonQuery方法

 - 调用一个返回静态结果的SQL Server存储过程
       -  创建Sqlcommandr实例
       - 将其CommandText属性设置为这个存储过程的名字
       - 将其Connection属性设置为一个有效的Sqlconnection实例
       - 调用这个命令对象的ExecuteScalar方法，捕获返回值


   ##    代码实例：

		      /*对于对数据库采取操作但不返回存储数据的服务*/
			string sqlText = @"UPDATE WorkTable SET ProcessedOn = GETDATE() WHERE ProcessedOn IS NULL";
			
           SqlCommand dataAction = new SqlCommand(sqlText, linkToDB);
           dataAction.ExecuteNonQuery();
		
		       /*对于返回单个值的查询*/
		 string sqlText1 = "select count(*) from WorkTable";
		 
         SqlCommand dataAction1 = new SqlCommand(sqlText1, linkToDB);
         
         int total = (int)dataAction1.ExecuteScalar();//返回单个值

	           /*对于返回数据行的操作*/
	           string sqlText2 = "select ID,FullName,ZipCode from Customer";
	           
            SqlCommand dataAction2 = new SqlCommand(sqlText2, linkToDB);
            
            SqlDataReader scanCustomer = dataAction2.ExecuteReader();//一次返回一个数据行
            
            if (scanCustomer.HasRows)
                while (scanCustomer.Read());

              /*对于访问字段的值，用访问器*/
              result = scanCustomer[0];//从起始位置查找
               result = scanCustomer[“ID”];//按列名称查找
	       scanCustomer.NextResult();//若有多个列的返回时

              /*查询中存在参数时，用@标识符替代*/
              string test = @"update Employee set Salary = @NewSalary where ID = @EmployeeID"；
              
           SqlCommand salaryUpadate = new SqlCommand(test, linkToDB);
           
            paramValue.Value = 50000m;//设置参数的值
            
            salaryUpadate.Parameters.Add(paramValue);//获取参数的值到原来的字符串中
            
             salaryUpadate.Parameters.AddWithValue("@NewSalary", 50000m);//另外一种实例化方式，用于简单的参数时
             
             salaryUpadate.ExecuteNonQuery();

           /*对于存储过程的查询时*/
           string test1 = "dbo.AddLocation";//创建存储过程的字符串
           
            SqlCommand locationCommand = new SqlCommand(test1, linkToDB);
            
            locationCommand.CommandType = CommandType.StoredProcedure;//把数据库命令的类型设置成存储过程
            
	
	
	 
	 

    
