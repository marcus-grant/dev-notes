How to Setup MongoDB for MERN Projects
======================================

*A brief guide based on MongoDB's own [guide][1] on setting up MongoDB for a standard MERN stack project*

The app uses [Javascript/Node.js driver][2] to access MongoDB

The App - MongoPop
------------------

This can be quite a useful app to have around. It will help test out and get exercise with MongoDB. After supplying it with the database connection information *(as displayed in the MongoDB Atlas GUI)* MongoPop provides these features:
* Accept your username & password and create the full MongoDB connect string
  * This will permit using it to connect to the database
* Populate your chosen MongoDB collection with bulk data
  * In this case [Mockeroo][3] is used to provide mock data
* Count number of documents
* Read sample documents
* Apply bulk changes to selected documents

Installing MongoDB
------------------

Because in some situations sensitive data is stored in the database it might be good to learn how to setup MongoDB without the usual methods like [MongoDB Atlas][4]. If that's good enough for the intended purpose, use it, it gives quite a bit of database access and storage for free.

### Install MongoDB itself

If on Ubuntu, these steps will get it installed, otherwise find out how to install it on the current system. MongoDB is already included in Ubuntu's repositories, however it's rarely up-to-date due to its 6 month release cycle, so it's better to install it directly from MongoDB themselves. First, Ubuntu ensures authenticity of added repositories by verifying it against a GPG key, so first the GPG key must be added.

```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
```

Now add the MongoDB repository so `apt` knows where to install and upgrade the package.

```
echo "deb [ arch=amd64,arm64 ] http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```

Then update `apt`'s package lists

```
sudo apt-get update
```

### Install MongoDB from Repository

Now it's possible to install the `mongodb-org` package using `apt`.

```
sudo apt-get install mongodb-org
```

Verify it has started with a `sudo systemctl status mongod`. Then if MongoDB is intended to startup with the system, enable it at startup. `sudo systemctl enable mongod`

> Note: WSL doesn't have full service support, but according to this stack overflow [response][6] it might not matter, just use `sudo service mongod start` instead

> Note: Another potential WSL solution could be to follow aseering's response on WSL's github [issues][7]




References
----------

[1]: https://www.mongodb.com/blog/post/the-modern-application-stack-part-2-using-mongodb-with-nodejs "MongoDB: The Modern Application Stack – Part 2: Using MongoDB With Node.js"
[2]: https://mongodb.github.io/node-mongodb-native/?_ga=2.7114098.1530309310.1513291809-1307756207.1513291809 "JavaScript/Node.js MongoDB driver"
[3]: https://www.mockaroo.com/ "Mockaroo.com - Realistic Data Generator"
[4]: https://www.mongodb.com/cloud "MongoDB Cloud Service"
[5]: https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-mongodb-on-ubuntu-16-04 "Digital Ocean: Install and Secure MongoDB on Ubuntu 16.04"
[6]: http://bit.ly/2CoU38q "StackOverflow: WSL & MongoDB"
[7]: http://bit.ly/2Cq4MiO "Github/Microsoft/WSL: MongoDB on Windows Bash Issue"

1. [MongoDB: The Modern Application Stack – Part 2: Using MongoDB With Node.js][1]
2. [JavaScript/Node.js MongoDB driver][2]
3. [Mockaroo.com - Realistic Data Generator][3]
4. [MongoDB Cloud Service][4]
5. [Digital Ocean: Install and Secure MongoDB on Ubuntu 16.04][5]
6. [StackOverflow: WSL & MongoDB][6]
7. [Github/Microsoft/WSL: MongoDB on Windows Bash Issue][7]
