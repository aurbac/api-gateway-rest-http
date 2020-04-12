## REST API with API Gateway

### Create API Server

1\. Create an EC2 instance (Ubuntu Server 18.04 LTS) with HTTP access and inside SSH execute the following commands.

``` bash
sudo apt-get update -y
sudo apt install python-pip python-virtualenv git nginx -y
sudo rm /etc/nginx/sites-enabled/default
sudo wget -O /etc/nginx/sites-available/flask https://raw.githubusercontent.com/aurbac/flask-notes/master/nginx-flask
sudo ln -s /etc/nginx/sites-available/flask /etc/nginx/sites-enabled/flask
sudo /etc/init.d/nginx restart

git clone https://github.com/aurbac/flask-notes.git
cd flask-notes/
virtualenv env
source env/bin/activate
pip install flask gunicorn
gunicorn index:app &
```

### Create API Gateway for Notes API

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. If this is your first time using API Gateway, you see a page that introduces you to the features of the service\. Choose **Get Started**\. When the **Create Example API** popup appears, choose **OK**\.

   If this is not your first time using API Gateway, choose **Create API** and choose **REST API**\.

1. Create an empty API as follows:

   1. Under **Choose the protocol**, choose **REST**\.

   1. Under **Create new API**, choose **New API**\.

   1. Under **Settings**:
      + For **API name**, enter **Notes**\.
      + If desired, enter a description in the **Description** field; otherwise, leave it empty\.
      + Leave **Endpoint Type** set to **Regional**\.

   1. Choose **Create API**\.