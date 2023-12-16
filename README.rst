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

Começo rápido
==========

.. início rápido

Neste tutorial, você usará o utilitário de linha de comando ``chalice``
para criar e implantar uma API REST básica. Este início rápido usa Python 3.7,
mas o AWS Chalice oferece suporte a todas as versões do python suportadas pelo AWS Lambda,
que inclui python3.6, python3.7, python3.8, python3.9.
Você pode encontrar as versões mais recentes do python no
`Página de download do Python <https://www.python.org/downloads/>`_.

Para instalar o Chalice, vamos primeiro criar e ativar um ambiente virtual
em python3.7::

    $ python3 --version
    Python 3.7.3
    $ python3 -m venv venv37
    $ . venv37/bin/activate

A seguir vamos instalar o Chalice usando ``pip``::

    $ python3 -m pip install chalice

Você pode verificar se o chalice está instalado executando::

    $ chalice --help
    Usage: chalice [OPTIONS] COMMAND [ARGS]...
    ...


Credenciais
-----------

Antes de implantar um aplicativo, certifique-se de ter
credenciais configuradas. Se você já configurou seu
máquina para executar o boto3 (o AWS SDK for Python) ou a AWS CLI
você pode pular esta seção.

Se esta é a primeira vez que configura credenciais para AWS, você
pode seguir estas etapas para começar rapidamente::

    $ mkdir ~/.aws
    $ cat >> ~/.aws/config
    [default]
    aws_access_key_id=YOUR_ACCESS_KEY_HERE
    aws_secret_access_key=YOUR_SECRET_ACCESS_KEY
    region=YOUR_REGION (such as us-west-2, us-west-1, etc)

Se você quiser mais informações sobre todos os métodos suportados para
configuração de credenciais, consulte o
`boto3 docs
<http://boto3.readthedocs.io/en/latest/guide/configuration.html>`__.


Criando seu projeto
---------------------

A próxima coisa que faremos é usar o comando ``chalice`` para criar um novo
projeto::

    $ chalice new-project helloworld

Isso criará um diretório ``helloworld``. CD para isso
diretório. Você verá que vários arquivos foram criados para você::

    $ cd helloworld
    $ ls -la
    drwxr-xr-x   .chalice
    -rw-r--r--   app.py
    -rw-r--r--   requirements.txt

Você pode ignorar o diretório ``.chalice`` por enquanto, os dois arquivos principais
nos concentraremos em ``app.py`` e ``requirements.txt``.

Vamos dar uma olhada no arquivo app.py:

.. code-block:: python

    from chalice import Chalice

    app = Chalice(app_name='helloworld')


    @app.route('/')
    def index():
        return {'hello': 'world'}


O comando ``new-project`` criou um aplicativo de amostra que define um
visão única, ``/``, que quando chamada retornará o corpo JSON
``{"olá": "mundo"}``.


Implantando
---------

Vamos implantar este aplicativo. Verifique se você está no ``helloworld``
diretório e execute ``chalice deploy``::

    $ chalice deploy
    Creating deployment package.
    Creating IAM role: helloworld-dev
    Creating lambda function: helloworld-dev
    Creating Rest API
    Resources deployed:
      - Lambda ARN: arn:aws:lambda:us-west-2:12345:function:helloworld-dev
      - Rest API URL: https://abcd.execute-api.us-west-2.amazonaws.com/api/

Agora você tem uma API funcionando usando API Gateway e Lambda::

    $ curl https://qxea58oupc.execute-api.us-west-2.amazonaws.com/api/
    {"hello": "world"}

Tente fazer uma alteração no dicionário retornado do ``index()``
função. Você pode reimplantar suas alterações executando ``chalice deploy``.

.. quick-start-end

Próximos passos
----------

Agora você criou seu primeiro aplicativo usando ``chalice``. Você pode fazer
modificações em seu arquivo ``app.py`` e execute novamente ``chalice deploy`` para
reimplantar suas alterações.

Neste ponto, existem vários próximos passos que você pode tomar.

* `Tutoriais <https://aws.github.io/chalice/tutorials/index.html>`__
  - Escolha entre vários tutoriais guiados que irão
   dar-lhe exemplos passo a passo de vários recursos do Chalice.
* `Tópicos <https://aws.github.io/chalice/topics/index.html>`__ - Profundo
   mergulhe na documentação sobre áreas específicas do Chalice.
 Isso contém documentação mais detalhada do que os tutoriais.
* `API Referência <https://aws.github.io/chalice/api.html>`__ - Nível baixo
   documentação de referência sobre todas as classes e métodos que fazem parte do
   API pública do Chalice.

Se você já experimentou o Chalice e gostaria de limpá-lo, você pode
use o comando ``chalice delete``, e o Chalice irá deletar todos os recursos
criado ao executar o comando ``chalice deploy``.

::

    $ chalice delete
    Deleting Rest API: abcd4kwyl4
    Deleting function aws:arn:lambda:region:123456789:helloworld-dev
    Deleting IAM Role helloworld-dev
