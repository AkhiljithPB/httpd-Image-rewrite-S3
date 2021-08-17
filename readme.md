# Website using S3 bucket to store Image contents.

-A bucket is similar to a folder on your computer and the only limitation of a bucket is that you must have a unique name across all of aws. 

Here I'm using an S3 bucket to store images of my website which will be easy to manage them.

### Required packages for installation:

- yum install httpd -y
- systemctl start httpd
- systemctl enable httpd -y

I'm already website "cafe.tkhouse.tech" which running on an EC2 instance with apache webserver. 
What I am doing here is moving the "img" directory to a S3 bucket and modifying the apache configuration file to serve the images from the bucket.


### Step1: Copying contents from "img" to S3Bucket using awscli command.

Copying contents of "img" directory to my newly created bucket "tkhouse-img"
```sh
[root@ip-172-31-13-11 html]# aws s3 sync img s3://tkhouse-img
upload: img/about-1.png to s3://tkhouse-img/about-1.png
upload: img/hot-espresso.png to s3://tkhouse-img/hot-espresso.png
upload: img/smoothie-2.png to s3://tkhouse-img/smoothie-2.png
upload: img/about-2.png to s3://tkhouse-img/about-2.png
upload: img/iced-cappuccino.png to s3://tkhouse-img/iced-cappuccino.png
upload: img/smoothie-1.png to s3://tkhouse-img/smoothie-1.png
upload: img/iced-espresso.png to s3://tkhouse-img/iced-espresso.png
upload: img/iced-latte.png to s3://tkhouse-img/iced-latte.png
upload: img/smoothie-3.png to s3://tkhouse-img/smoothie-3.png
upload: img/special-01.jpg to s3://tkhouse-img/special-01.jpg
upload: img/special-03.jpg to s3://tkhouse-img/special-03.jpg
upload: img/special-06.jpg to s3://tkhouse-img/special-06.jpg
upload: img/special-04.jpg to s3://tkhouse-img/special-04.jpg
upload: img/smoothie-4.png to s3://tkhouse-img/smoothie-4.png
upload: img/hot-cappuccino.png to s3://tkhouse-img/hot-cappuccino.png
upload: img/special-02.jpg to s3://tkhouse-img/special-02.jpg
upload: img/hot-americano.png to s3://tkhouse-img/hot-americano.png
upload: img/special-05.jpg to s3://tkhouse-img/special-05.jpg
upload: img/hot-latte.png to s3://tkhouse-img/hot-latte.png
upload: img/iced-americano.png to s3://tkhouse-img/iced-americano.png
[root@ip-172-31-13-11 html]#
```

### Step2: Make the bucket for public access and add IAM Bucket Policy To Make Bucket Public.

Firstly we have to edit the Block public access option from permissions for my bucket "tkhouse-img" to made access to public.
After that, adding below Bucket policy from permissions to allow everyone one to access objects from my bucket tkhouse-img.

```sh
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::tkhouse-img/*"
            ]
        }
    ]
}
```
Finally we have to modify the apache config file to serve the img directory from this bucket.
```
RewriteEngine On
RewriteRule ^/img/(.*)$ https://tkhouse-img.s3.ap-south-1.amazonaws.com/$1 [L]
```
Restart apache.
```sh
systemctl restart httpd.service
```
You can open the webiste image on a browser tab and see the image url will be pointed to your S3 bucket like "https://tkhouse-img.s3.ap-south-1.amazonaws.com/iced-americano.png".

![alt text](https://github.com/AkhiljithPB/httpd-Image-rewrite-S3/blob/fed36dd6f6421866400c20fcfc36216337a1e389/web.png?raw=true)
![alt text](https://github.com/AkhiljithPB/httpd-Image-rewrite-S3/blob/fed36dd6f6421866400c20fcfc36216337a1e389/coffee.png?raw=true)
