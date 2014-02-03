Heroku buildpack: Node.js
=========================

This is a custom Heroku buildpack for Node.js applications that run on the [Cedar](http://devcenter.heroku.com/articles/cedar) runtime stack on Heroku.
It uses [NPM](http://npmjs.org/) and [SCons](http://www.scons.org/).

Quick start
-----------

Example usage:

    $ ls
    Procfile  package.json  web.js

    $ heroku create --stack cedar --buildpack http://github.com/liquid/heroku-buildpack-nodejs.git

    $ git push heroku master
    ...
    -----> Heroku receiving push
    -----> Fetching custom buildpack
    -----> Node.js app detected
    -----> Vendoring node 0.6.13
    -----> Installing dependencies with npm 1.1.0-2
           express@2.1.0 ./node_modules/express
           ├── mime@1.2.2
           ├── qs@0.3.1
           └── connect@1.6.2
           Dependencies installed

The buildpack will detect your app as Node.js if it has the file `package.json` in the root.  It will use NPM to install your dependencies, and vendors a version of the Node.js runtime into your slug.  The `node_modules` directory will be cached between builds to allow for faster NPM install time.

Customization
-------------

You can create your own buildpack in order to use different versions of Node.js and/or NPM. To change the vendored binaries for Node.js, NPM, and SCons, use the helper scripts in the `support/` subdirectory.

You are going to need the following:

* An S3 enabled AWS account to store your binaries in.
* Your application must use NPM to manage dependencies. (See `package.json` on the [Heroku Dev Center](http://devcenter.heroku.com/articles/node-js#declare_dependencies_with_npm)).
* Obviously a Heroku user account. [Signup is free and instant](https://api.heroku.com/signup).

Workflow:

* Fork this buildpack on Github and clone it to somewhere in order to make changes to it
* Set the nexessary environment variables in your shell:

        $ export AWS_ID="YOUR-AWS-ID" AWS_SECRET="YOUR-AWS-SECRET" S3_BUCKET="YOUR-S3BUCKET-NAME"

* Create your S3 bucket (in case it doesn't exist yet):

        $ s3 create $S3_BUCKET
* Customise your version of Node that you want to use by running `./support/package_node` with the desired version of Node. The script will compile Node and push the binaries ready onto your S3 bucket:

        $ ./support/package_node 0.6.13

* Open `bin/compile` in your editor, and change the following lines:

        NODE_VERSION="0.6.13"
		S3_BUCKET=zzz
* Commit and push the changes to your buildpack to your Github fork
* Create a test application that makes use of your custom buildpack and push to it:

        $ heroku create --buildpack <your-github-url>
* You should see:

        -----> Vendoring node 0.6.13
        -----> Installing dependencies with npm 1.1.0-2

[![Bitdeli Badge](https://d2weczhvl823v0.cloudfront.net/liquid/heroku-buildpack-nodejs/trend.png)](https://bitdeli.com/free "Bitdeli Badge")

