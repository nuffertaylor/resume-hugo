+++
title="How to Connect to Cockroach DB in AWS Lambda through Python"
date = "2022-09-19"
+++

If you can successfully access Cockroach DB via python locally, but are struggling to access it on your AWS Lambda, you might be hitting the same brick wall as me. Maybe you've seen errors like:

    sslmode value \"verify-full\" invalid when SSL support is not compiled in

or

    root certificate file \"/home/sbx_user1051/.postgresql/root.crt\" does not exist\nEither provide the file or change sslmode to disable server certificate verification.\n",

I spent many hours figuring this out, with scant search results coming to the rescue. Now that I've figured it out, here's the process:

1) Get the right psycopg2 library

clone this github project:

https://github.com/jkehler/awslambda-psycopg2.git

go to the "with_ssl_support" directory (required to connect to cockroach_db), and find the version of psycopg2 you're using. I was using 3.8.

Copy that folder into your lambda project directory. Rename the folder to just be "psycopg2" (so the version number is no longer appended)

2) Get the root.crt SSL certificate

on the cockroach db website, once signed in, click connect->option(python)->Download CA Cert.
You'll see a curl command that looks like this. Use it to download the root.crt you'll need for an ssl connection.
{{<figure src="https://i.imgur.com/HNny2cq.png" class="center">}}

Copy that root.crt file into your lambda project directory as well.

When you connect to your cockroach database, alongside the normal connection url, add the parameter "sslrootcert" and provide the path to root.crt. Because we copied it into the root directory, you can just do it like so

    self.conn = psycopg2.connect(db_url, sslrootcert="./root.crt")

Now we're ready! Just package everything in this directory up

    zip -r lambda.zip .

and upload it to AWS. I upload it using the aws cli like so:

    aws lambda update-function-code --function-name put_your_lambda_name_here \                    
    --zip-file fileb://~/put_your_directory_here/lambda.zip

but you can also do it via the website GUI if you prefer.

Also, you're probably a genius (unlike me) and knew that psycopg2 doesn't return a list of objects on query, but a list of tuples. In case you didn't know, you may need to format each tuple to an object manually if you want to access any of the attributes via key. Thankfully, the tuples are returned in the column order as specified by the table's CREATE statement, so referring to that, you should be able to map the data with ease.