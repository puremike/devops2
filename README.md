# PROJECT 2: Re-Architecting a Web Application Using AWS Cloud-Native Services

This guide outlines how I re-architected a web application to leverage AWS Cloud-Native Services, replacing traditional EC2-based hosting for components like MySQL, Tomcat, Memcached, and RabbitMQ. This deployment ensures scalability, cost efficiency, and high availability.

---

## Cloud-Native Services Utilized

- **Amazon RDS**: MySQL Database
- **Amazon MQ**: Message Broker (RabbitMQ)
- **Amazon ElastiCache**: Database Caching (Memcache)
- **Elastic Beanstalk**: Application Deployment
- **Amazon CloudFront**: Content Delivery Network
- **EC2**: Instance for initial database setup

---

## Steps to Re-Architect the Application

### 1. Clone the Project from GitHub

- Pulled the project source code from GitHub:
  ```bash
  git clone -b awsrefactor https://github.com/hkhcoder/vprofile-project.git
  ```
- Reviewed and understood the project structure for deployment.

---

### 2. Understand the Project Components

Instead of hosting all services on EC2 instances, I leveraged AWS Cloud-Native Services:

- **Amazon RDS** for MySQL
- **Amazon MQ** for RabbitMQ
- **Amazon ElastiCache** for Memcache
- **Elastic Beanstalk** for Tomcat application hosting
- **Amazon CloudFront** for global content delivery

---

### 3. Configure AWS Security Groups

- Created a security group (**vprofile-backend-services**) for the backend services, including RDS, MQ, and ElastiCache.
- **Inbound Rules**:
  - **All Traffic**: Accessible only within its own security group.

---

### 4. Deploy Amazon RDS (MySQL)

- **Step 1**: Created a parameter group and subnet group for custom instance configurations.
- **Step 2**: Launched the RDS instance with MySQL.
- **Step 3**: Used an EC2 instance to initialize the RDS database:
  ```bash
  apt-get install mysql-client git
  git clone -b awsrefactor https://github.com/hkhcoder/vprofile-project.git
  mysql -h <rds-endpoint> -u admin -p accounts < src/main/resources/db_backup.sql
  ```

---

### 5. Deploy Amazon MQ (RabbitMQ)

- Created an Amazon MQ instance with a parameter group and subnet group for customization.

---

### 6. Deploy Amazon ElastiCache (Memcache)

- Launched an ElastiCache instance with Memcache for database caching.

---

### 7. Update Application Configuration

- Updated `application.properties` with the host addresses of RDS, ElastiCache, and MQ:

  ```properties
  # JDBC Configuration
  jdbc.url=jdbc:mysql://<rds-endpoint>:3306/accounts
  jdbc.username=admin
  jdbc.password=<password>

  # Memcached Configuration
  memcached.active.host=<elasticache-endpoint>:11211

  # RabbitMQ Configuration
  rabbitmq.address=<mq-endpoint>:5671
  rabbitmq.username=rabbit
  rabbitmq.password=<password>
  ```

---

### 8. Build the Application

- Built the project artifact locally:
  ```bash
  mvn install
  ```

---

### 9. Deploy with AWS Elastic Beanstalk

- Created an Elastic Beanstalk environment for Tomcat applications:
  - Ensured scalability with load balancers, target groups, and auto-scaling.
- Deployed the application artifact to the Beanstalk environment.

---

### 10. Configure Amazon CloudFront

- Created a CloudFront distribution pointing to the Elastic Beanstalk load balancer.
- Enabled global content delivery with edge locations for improved user experience.
- Configured domain redirection using the CloudFront hostname.

---

## Conclusion

This project successfully re-architected the application by replacing traditional EC2-based hosting with AWS Cloud-Native Services, improving scalability, availability, and performance.
