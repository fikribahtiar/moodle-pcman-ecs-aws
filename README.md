# Modernize Moodle LMS with AWS serverless containers

This repository contains an [AWS Cloud Development Kit (AWS CDK)](https://aws.amazon.com/cdk/) application to deploy highly-available, elastic, and scalable Moodle LMS application using containers technology on AWS by leveraging [Amazon Elastic Container Services (Amazon ECS)](https://aws.amazon.com/ecs/) and [AWS Fargate](https://aws.amazon.com/fargate/).

For more details and instructions on how to deploy, please visit the following blog post: https://aws.amazon.com/blogs/publicsector/modernize-moodle-lms-aws-serverless-containers/

Langkah-langkah
1. docker build -t moodle-image src/image/src
2. aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 767397813992.dkr.ecr.us-east-1.amazonaws.com
3. aws ecr create-repository --repository-name moodle-image --region us-east-1
4. docker tag moodle-image:latest 767397813992.dkr.ecr.us-east-1.amazonaws.com/moodle-image:latest
5. docker push 767397813992.dkr.ecr.us-east-1.amazonaws.com/moodle-image:latest

Buat domain baru, 
buka route 53, create domain, masukan nama domain nya
dan copy NS domain ke dns provider tempat anda membeli domain, dan rubah semua NS nya menjadi NS aws

sebelum buat sertifikat ssl, pastikan dulu semua region sama
caranya masuk ke certificate manager
lalu pilih region dulu sesuaikan dengan yang lain misalnya us-east-1 (catatan cloudfront hanya tersedia di us-east-1 di zona lain tidak ada)
request certificate, masukin domain dan subdoamin nya jika ada
tunggu sampai status nya dari pending menjadi issued (catatan )
lalu import CNAME to route 53 supaya tidak copy manual

6. edit file cdk.json
"app-config/albCertificateArn": "arn:aws:acm:us-east-1:767397813992:certificate/71e87af1-6165-42f9-8ced-387b045c425d",
"app-config/cfCertificateArn": "arn:aws:acm:us-east-1:767397813992:certificate/71e87af1-6165-42f9-8ced-387b045c425d",
"app-config/cfDomain": "simplepro.id",
"app-config/moodleImageUri": "767397813992.dkr.ecr.us-east-1.amazonaws.com/moodle-image:latest",

7. Pada file image\src/libmoodle.sh jangan lupa ganti dari CRLF menjadi LF untuk menghindari error di log \r dicontainer karena scrypt ini dibuat di env Windows
8. Masuk ke Folder cdk
cd src/cdk
9. install node 20 (jika belum)
10. pada folder src/cdk jalankan ini (jika belum)
npm install
11. lalu pada directory bersama jalankan ini
cdk bootstrap
12. lalu jalankan ini
cdk deploy --all

13. ketika deploy nya udah selesai, copy pada Service ALB (Load Balancer) dns name nya
moodle-ecs-alb-1972981379.us-east-1.elb.amazonaws.com
14. buka route 53 dan pilih domain
15. tambahkan record baru 
host : www.simplepro.id
type : CNAME
Value : moodle-ecs-alb-1972981379.us-east-1.elb.amazonaws.com

16. Untuk melihat password moodle bisa masuk ke halaman secret manager -> pilih moodlepasswordsecret -> pilih Retrieve secret value -> copy password string nya

17. jika mau menghapus semuanya jalankan ini 
cdk destroy --all

Referensi : https://aws.amazon.com/id/blogs/publicsector/modernize-moodle-lms-aws-serverless-containers/ 


