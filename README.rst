===========
AWS Chalice
===========

.. image:: https://badges.gitter.im/awslabs/chalice.svg
   :target: https://gitter.im/awslabs/chalice?utm_source=badge&utm_medium=badge
   :alt: Gitter
.. image:: https://readthedocs.org/projects/chalice/badge/?version=latest
   :target: http://aws.github.io/chalice/?badge=latest
   :alt: Documentation Status


.. image:: https://aws.github.io/chalice/_images/chalice-logo-whitespace.png
   :target: https://aws.github.io/chalice/
   :alt: Chalice Logo


Chalice é uma estrutura para escrever aplicativos sem servidor em python. Ele permite criar 
e implantar rapidamente aplicativos que usam o AWS Lambda. Ele fornece:

* Uma ferramenta de linha de comando para criar, implantar e gerenciar seu aplicativo
* Uma API baseada em decorador para integração com Amazon API Gateway, Amazon S3, Amazon SNS, Amazon SQS e outros serviços da AWS.
* Geração automática de políticas de IAM


Você pode criar APIs Rest:

.. code-block:: python

    from chalice import Chalice

    app = Chalice(app_name="helloworld")

    @app.route("/")
    def index():
        return {"hello": "world"}

Tarefas executadas periodicamente:

.. code-block:: python

    from chalice import Chalice, Rate

    app = Chalice(app_name="helloworld")

    # Automatically runs every 5 minutes
    @app.schedule(Rate(5, unit=Rate.MINUTES))
    def periodic_task(event):
        return {"hello": "world"}


Você pode conectar uma função lambda a um evento S3:

.. code-block:: python

    from chalice import Chalice

    app = Chalice(app_name="helloworld")

    # Whenever an object is uploaded to 'mybucket'
    # this lambda function will be invoked.

    @app.on_s3_event(bucket='mybucket')
    def handler(event):
        print("Object uploaded for bucket: %s, key: %s"
              % (event.bucket, event.key))

Bem como uma fila SQS:

.. code-block:: python

    from chalice import Chalice

    app = Chalice(app_name="helloworld")

    # Invoke this lambda function whenever a message
    # is sent to the ``my-queue-name`` SQS queue.

    @app.on_sqs_message(queue='my-queue-name')
    def handler(event):
        for record in event:
            print("Message body: %s" % record.body)


E vários outros recursos da AWS.

Depois de escrever seu código, basta executar ``chalice deploy``
e o Chalice cuida da implantação do seu aplicativo.

::

    $ chalice deploy
    ...
    https://endpoint/dev

    $ curl https://endpoint/api
    {"hello": "world"}

Instalado e funcionando em menos de 30 segundos.
Experimente este projeto e compartilhe seus comentários conosco aqui no Github.

A documentação está disponível
`aqui <http://aws.github.io/chalice/>`__.

Quickstart
==========

.. quick-start-begin

In this tutorial, you'll use the ``chalice`` command line utility
to create and deploy a basic REST API.  This quickstart uses Python 3.7,
but AWS Chalice supports all versions of python supported by AWS Lambda,
which includes python3.6, python3.7, python3.8, python3.9.
You can find the latest versions of python on the
`Python download page <https://www.python.org/downloads/>`_.

To install Chalice, we'll first create and activate a virtual environment
in python3.7::

    $ python3 --version
    Python 3.7.3
    $ python3 -m venv venv37
    $ . venv37/bin/activate

Next we'll install Chalice using ``pip``::

    $ python3 -m pip install chalice

You can verify you have chalice installed by running::

    $ chalice --help
    Usage: chalice [OPTIONS] COMMAND [ARGS]...
    ...


Credentials
-----------

Before you can deploy an application, be sure you have
credentials configured.  If you have previously configured your
machine to run boto3 (the AWS SDK for Python) or the AWS CLI then
you can skip this section.

If this is your first time configuring credentials for AWS you
can follow these steps to quickly get started::

    $ mkdir ~/.aws
    $ cat >> ~/.aws/config
    [default]
    aws_access_key_id=YOUR_ACCESS_KEY_HERE
    aws_secret_access_key=YOUR_SECRET_ACCESS_KEY
    region=YOUR_REGION (such as us-west-2, us-west-1, etc)

If you want more information on all the supported methods for
configuring credentials, see the
`boto3 docs
<http://boto3.readthedocs.io/en/latest/guide/configuration.html>`__.


Creating Your Project
---------------------

The next thing we'll do is use the ``chalice`` command to create a new
project::

    $ chalice new-project helloworld

This will create a ``helloworld`` directory.  Cd into this
directory.  You'll see several files have been created for you::

    $ cd helloworld
    $ ls -la
    drwxr-xr-x   .chalice
    -rw-r--r--   app.py
    -rw-r--r--   requirements.txt

You can ignore the ``.chalice`` directory for now, the two main files
we'll focus on is ``app.py`` and ``requirements.txt``.

Let's take a look at the ``app.py`` file:

.. code-block:: python

    from chalice import Chalice

    app = Chalice(app_name='helloworld')


    @app.route('/')
    def index():
        return {'hello': 'world'}


The ``new-project`` command created a sample app that defines a
single view, ``/``, that when called will return the JSON body
``{"hello": "world"}``.


Deploying
---------

Let's deploy this app.  Make sure you're in the ``helloworld``
directory and run ``chalice deploy``::

    $ chalice deploy
    Creating deployment package.
    Creating IAM role: helloworld-dev
    Creating lambda function: helloworld-dev
    Creating Rest API
    Resources deployed:
      - Lambda ARN: arn:aws:lambda:us-west-2:12345:function:helloworld-dev
      - Rest API URL: https://abcd.execute-api.us-west-2.amazonaws.com/api/

You now have an API up and running using API Gateway and Lambda::

    $ curl https://qxea58oupc.execute-api.us-west-2.amazonaws.com/api/
    {"hello": "world"}

Try making a change to the returned dictionary from the ``index()``
function.  You can then redeploy your changes by running ``chalice deploy``.

.. quick-start-end

Next Steps
----------

You've now created your first app using ``chalice``.  You can make
modifications to your ``app.py`` file and rerun ``chalice deploy`` to
redeploy your changes.

At this point, there are several next steps you can take.

* `Tutorials <https://aws.github.io/chalice/tutorials/index.html>`__
  - Choose from among several guided tutorials that will
  give you step-by-step examples of various features of Chalice.
* `Topics <https://aws.github.io/chalice/topics/index.html>`__ - Deep
  dive into documentation on specific areas of Chalice.
  This contains more detailed documentation than the tutorials.
* `API Reference <https://aws.github.io/chalice/api.html>`__ - Low level
  reference documentation on all the classes and methods that are part of the
  public API of Chalice.

If you're done experimenting with Chalice and you'd like to cleanup, you can
use the ``chalice delete`` command, and Chalice will delete all the resources
it created when running the ``chalice deploy`` command.

::

    $ chalice delete
    Deleting Rest API: abcd4kwyl4
    Deleting function aws:arn:lambda:region:123456789:helloworld-dev
    Deleting IAM Role helloworld-dev


Feedback
========

We'd also love to hear from you.  Please create any Github issues for
additional features you'd like to see over at
https://github.com/aws/chalice/issues.  You can also chat with us
on gitter: https://gitter.im/awslabs/chalice
