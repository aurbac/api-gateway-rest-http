## REST API with API Gateway

### Create API Server

1. Create an EC2 instance (Ubuntu Server 18.04 LTS) with HTTP access and inside SSH execute the following commands.\.

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

You can test with the following URLs:
+ **http://[your_server_name]/notes**
+ **http://[your_server_name]/notes/id/1**

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

1. Create the `notes` resource as follows:

   1. Choose the root resource \(**/**\) in the **Resources** tree\.

   1. Choose **Create Resource** from the **Actions** dropdown menu\.

   1. Leave **Configure as proxy resource** unchecked\.

   1. For **Resource Name**, enter **notes**\.

   1. Leave **Resource Path** set to **/notes**\.

   1. Leave **Enable API Gateway CORS** unchecked\.

   1. Choose **Create Resource**\.
 
### Create the method to retrieve a note

1. Create the `NoteItem` model as follows:

   1. Choose **Models** and choose **Create**\.

   1. For **Model name**, type **NoteItem**\.

   1. For **Content type**, type **application/json**\.

   1. For **Model description**, type **Model for note**\.

   1. For **Model schema**, type the following JSON Schema-compatible definition:\.

   ```
   {
       "title" : "NoteItem",
       "type" : "object",
       "properties" : {
           "note_id": { "type": "string" },
           "title": { "type": "string" },
           "description": { "type": "string" },
           "image_url": { "type": "string" }
       }
   }
   ```
   1. Choose **Create model**\.

1. Create the `notes-id` resource as follows:

   1. Choose the resource \(**/notes**\) in the **Resources** tree\.

   1. Choose **Create Resource** from the **Actions** dropdown menu\.

   1. Leave **Configure as proxy resource** unchecked\.

   1. For **Resource Name**, enter **notes-id**\.

   1. Leave **Resource Path** set to /notes/**{itemId}**\.

   1. Leave **Enable API Gateway CORS** unchecked\.

   1. Choose **Create Resource**\.

1. Create the `GET` method as follows:

   1. In the **Resources** list, choose /notes**/{itemId}**\.

   1. In the **Actions** menu, choose **Create method**\.

   1. Choose **GET** from the dropdown menu, and choose the checkmark icon\.

   1. For **Integration type** set to **HTTP**\.

   1. For **Endpoint URL**, type your URL **http://[your_server_name]/notes/id/{itemId}**\.

   1. Leave **Use Default Timeout** checked\.

   1. Choose **Save**\.

1. Update the **Method Response** as follows:

   1. In the **Resources** list, choose the method **GET** for /notes**/{itemId}**\.

   1. Choose **Method Response**\.

   1. Expand the HTTP Status **200**\.

   1. For **Response Body for 200** edit the Model for **application/json**, choose **NoteItem** and choose the checkmark icon\.

1. Update the **Integration Response** as follows:

   1. In the **Resources** list, choose the method **GET** for /notes**/{itemId}**\.

   1. Choose **Integration Response**\.

   1. Expand the method response status **200**\.
   
   1. Expand **Mapping Templates**\.
   
   1. Choose **application/json** \.
   
   1. For **Generate template** select **NoteItem** \.

   1. For **Model schema**, update with the following JSON:\.

   ```
   {
     "note_id" : $input.json('$.id'),
     "title" : $input.json('$.name'),
     "description" : $input.json('$.description'),
     "image_url" : $input.json('$.image'),
   }
   ```
   1. Choose **Save**\.

1. Test the method as follows:

   1. In the **Resources** list, choose the method **GET** for /notes**/{itemId}**\.

   1. Choose **Test**\.

   1. For **{itemId}** type **1** and choose **Test**\.

   1. If successful, **Response Body** is displayed\.