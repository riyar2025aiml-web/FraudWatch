# Detailed setup for Windows + IntelliJ + XAMPP

## 1. Install requirements

- JDK 17
- IntelliJ IDEA
- XAMPP with MySQL
- Postman

Verify Java:
```bash
java -version
```

Verify Maven through IntelliJ's Maven integration. A Maven installation is optional because IntelliJ can use the Maven Wrapper if one is present; this project can also be run with IntelliJ's Maven support.

## 2. Start MySQL

Open XAMPP Control Panel and click **Start** next to MySQL.

Default XAMPP settings used by this project:
- Host: localhost
- Port: 3306
- Username: root
- Password: empty

If yours differs, edit:
`src/main/resources/application.properties`

Example:
```properties
spring.datasource.username=root
spring.datasource.password=your_password
```

## 3. Open in IntelliJ

File → Open → select the `FraudWatch` folder.

IntelliJ should detect `pom.xml` and import Maven dependencies.

Select JDK 17:
File → Project Structure → Project SDK → Java 17.

## 4. Run

Open:
`src/main/java/com/fraudwatch/FraudWatchApplication.java`

Click the green Run button.

Wait until you see a message similar to:
`Started FraudWatchApplication`

Then visit:
`http://localhost:8080/`

## 5. MySQL database

The JDBC URL contains:
`createDatabaseIfNotExist=true`

So the database named `fraudwatch` is created automatically when the MySQL user has permission.

Hibernate creates/updates the tables using:
`spring.jpa.hibernate.ddl-auto=update`

You can inspect the database in:
XAMPP → MySQL → phpMyAdmin → `fraudwatch`

## 6. If port 3306 is already used

Change the XAMPP MySQL port and update the JDBC URL, for example:
```properties
spring.datasource.url=jdbc:mysql://localhost:3307/fraudwatch?createDatabaseIfNotExist=true&useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=Asia/Kolkata
```

## 7. If port 8080 is busy

Change:
```properties
server.port=8081
```
Then open `http://localhost:8081/`.

## 8. Reset demo database

For a clean demonstration, stop the application, delete the `fraudwatch` database from phpMyAdmin, start the application again, and the schema/demo records will be recreated.

## 9. Postman

Import/use these requests manually:

GET:
`http://localhost:8080/api/accounts`

POST:
`http://localhost:8080/api/accounts`
```json
{
  "name": "Demo User",
  "email": "demo@example.com",
  "balance": 50000
}
```

POST:
`http://localhost:8080/api/transactions`
```json
{
  "senderId": 1,
  "receiverId": 2,
  "amount": 15000,
  "description": "Demo suspicious transfer"
}
```

GET:
`http://localhost:8080/api/flagged-transactions`

PUT:
`http://localhost:8080/api/flagged-transactions/1/review`
```json
{
  "outcome": "BLOCKED",
  "reviewer": "Admin"
}
```

## 10. Common errors

### Access denied for root
Set the correct MySQL password in `application.properties`.

### Communications link failure
Make sure XAMPP MySQL is running and the port matches the JDBC URL.

### Java version error
Use JDK 17.

### Tables not appearing
Stop/restart the Spring Boot application after confirming MySQL is running.
