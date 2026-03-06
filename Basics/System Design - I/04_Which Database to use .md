# 🗄️ Which Database to use ??

## 🔄 Recap: The Two-Server Setup

Today we will see which database we should use. Previously, we had a single server system, so what did we do? We created two servers. One server was handling our traffic, and we had kept the second server for the management of the DB.

And that was done; that problem was solved. Now we are looking at which database we should use according to our service. So first, let's see what types of databases exist.

---

## 🗃️ Types of Databases

So we have two types of databases right now:

1. **Relational Database**
2. **Non-Relational Database**

```text
    _______
   /       \
  |   DB    |
  |         |
   \_______/

```

Let's learn about them and know their differences first. I have already created the differentiations for both.

### ⚖️ Relational DB vs. Non-Relational DB

```text
          Relational DB               |       Non-Relational DB
    ----------------------------------+----------------------------------
    - RDBMS                           | - NoSQL Databases
      (Relational Database            |
       Management System)             | - Example :-
      or,                             |   Cassandra,
      SQL Database                    |   Amazon DynamoDB
                                      |   etc.
                                      |
    - Example :- MYSQL,               | - Grouped into 4 categories :-
      Oracle, PostgreSQL.             |   • Graph Stores
                                      |   • Key-value stores
    - Represent and Store             |   • column stores
      data in tables & rows           |   • Document stores
                                      |
    - Can perform JOIN                | - JOIN operation
      operations using SQL            |   not supported  :(
      on different tables.            |

```

**Relational Database (SQL):** It is also called RDBMS; it is very popular by its name. Its full form is Relational Database Management System, or it is also called SQL Database because the SQL language is used to query on the tables. I think very much many of you are already familiar with SQL. And while looking for examples, let's assume MySQL, Oracle, PostgreSQL are SQL databases. So these are examples of RDBMS or Relational Databases.  And in a Relational Database, whatever data there is, we store it or represent it in the form of tables and rows; you must know this. We write an SQL query on that itself, for example, `SELECT * FROM EmployeeTable` right? And it is an obvious thing that if we have tables, we can apply JOIN operations on the tables. For example, you have five tables, and you have to extract an information that can only be extracted by combining two tables, so what can you do? You can JOIN them with a single SQL operation.

**Non-Relational Database (NoSQL):**
Next is Non-Relational Database. It is also called NoSQL Database. I think it is more popular by this name, NoSQL Database System. For example, Cassandra, Amazon DynamoDB, okay? And it is grouped into 4 categories. Whatever most databases have come out so far, whatever are currently running in the market, we can group them into four categories: Graph Stores, Key-value stores, Column stores, and Document stores. Later on in the lectures, we will understand all these things, but you just need to know that they have been grouped into four categories. And since there is no concept of a table here, we cannot apply a JOIN operation.

---

## 🤔 Which one to use?

Now the question comes: which database do we need to use? Which one to use here?

For most developers, Relational DB is suitable because Relational DB has been used for the last many years. Historically, it has worked really well. Relational databases are heavily used by most of the people in most of the services.

But, there are many cases in which a Relational DB is not suitable. There are many such cases.

### 💡 When to think about Non-Relational DB?

So when should we think about using a Non-Relational Database?

* **a) Your application requires super low latency.** If you need very, very low latency, then we can think of a NoSQL database.
* **b) When your data is unstructured.** It is unstructured; we don't need to define any schema, like what will be the column name, what will be the column type, what will be the table, there is no need for it. So it means unstructured data. If the data is unstructured, we can use a Non-Relational Database.
* **c) You only need to serialize and deserialize data (JSON, XML, YAML etc.).** Meaning you don't need any schema for it; there is no need for structuring in it. Examples of what you serialize and deserialize are JSON, XML, or YAML files, etc. In such a case, we can use a Non-Relational database.
* **d) When you need to store MASSIVE amount of data.** And the most important reason why people started thinking about Non-Relational databases: if you need a lot of data, when you need to store a MASSIVE amount of data. Then we can use a Non-Relational database.

> **Key Takeaway:** Another special thing about a NoSQL or Non-Relational database is that there is no restriction in it that your data must be of a specific type. You can put everything together. Now, in a Relational database, we had to specify the data type as well, that it's a `varchar`, `int`, `string`, etc. But in a Non-Relational database, we don't need anything like that; you can add different types, you can make an array and add it.

And generally, assume that Non-Relational databases are often used in large quantities of complex and diverse data where we don't need to organize it. When there is going to be very massive data, people generally move towards Non-Relational databases.

**Example Scenario:**
Take an example: a large online store might prefer a database in which a customer has their own document containing all the information. Their name, their address, their order history, their credit card information, right? Very huge data was looking like that. Similarly, when there are, for example, millions of users of a service, so we have to store a massive amount of data, in that case, obviously, we will think about a Non-Relational database or NoSQL database.

---

## ⏭️ Next Up

We will now see how we do scaling, what are the types of scaling, which is very important for an interview.