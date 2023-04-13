# Api-aws-postgree

Estudos pra não enferrujar e também pra aprender na prática como mexer em alguns recursos da AWS graças ao Bootcamp disponibilizado pela @Stack_tecnologias no youtube.

<h2>Bootcamp de Engenharia de Dados</h2>

<h3>Setup AWS</h3>
Crie uma conta na AWS (Conta root);
<br>
Crie uma conta com permissão de administrador para ser a conta usada para projeto;
<br>
Crie um servidor PostgreSQL na versão 13 usando o Amazon RDS;
<br>
Clique no Security Groupo e libere o acesso a porta 5432 vindo do seu ip público;

<h3>Setup Ingestion to RDS</h3>
Edite o arquivo .env com as informções do Amazon RDS;
<br>
Instale todas as bibliotecas necessárias para rodar a aplicação de ingestão com o comando:
<br>
pip install -r requirements.txt
<br>
Execute a aplicação app-ingestion-sql
<br>
Conecte no banco de dados usando algum cliente de banco de dados, por exemplo o dbeaver e verifique se as tabelas foram criadas.

<h3>Setup Ingestion to Data Lake</h3>
Edite o arquivo .env informando as credencias de chave de acesso, por exemplo:

AWS_ACCESS_KEY_ID=AKIA4WFGE4SB25sdfadf
AWS_SECRET_ACCESS_KEY=mwXXs8NjLwI83fnxrZ7rm4zsQHEftY3adfafd

Crie um bucket na aws, no meu caso criei raw-bootcampde-872226808963
<br>
Edit o arquivo .env informando o nome do bucket criado na variável BUCKET_NAME
<br>
Execute a aplicação app-mobile-customers.py
<br>
Verifique se os arquivos .json estão sendo inseridos no bucket.


Importante:
<br>
Interrompa o banco de dados RDS.
<br>
DMS Setup
<br>
Crie uma instância de replicação.
<br>
Crie os endpoints target e source.
<br>
Use o código abaixo para criar a police/role para o endpoint target no s3.
<br>
Documentação do s3 como target endpoint: https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.S3.html

#{


    "Version": "2012-10-17"    
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:DeleteObject",
                "s3:PutObjectTagging"
            ],
            "Resource": [
                "arn:aws:s3:::raw-bootcampde-872226808963/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::raw-bootcampde-872226808963"
            ]
        }
    ]
}#

<br>
Adicione a opção AddColumName no endpoint target.
<br>
Crie uma task para iniciar o DMS.

<h3>Setup EMR</h3>
Libere o acesso na porta 22. Para isso clique em EC2 security group clique no security group e libere a porta 22 para o IP.
<br>
Acesso via ssh com Putty:
<br>
Download PuTTY.exe to your computer from: https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html
<br>
Start PuTTY.
<br>
In the Category list, choose Session.
<br>
In the Host Name field, enter hadoop@ec2-52-23-204-207.compute-1.amazonaws.com
<br>
In the Category list, expand Connection > SSH and then choose Auth.
<br>
For private key file for authentication, choose Browse and select the private key file (key-boot02.ppk) that you used to launch the cluster.
<br>
Select Open.
<br>
Select Yes to dismiss the security alert.
<br>
Copie a aplicação para o servidor do Amazon EMR usando o WinScp (Windows) ou o scp (linux), para linux segue um exemplo:
<p>



<i>scp -i ~/Downloads/pair-bootcamp.pem job-spark-app-emr-redshift.py hadoop@ec2-54-90-3-194.compute-1.amazonaws.com:/home/hadoop/</i>


Para executar o cluster Spark use o comando abaixo:


<i>spark-submit --packages io.delta:delta-core_2.12:2.0.0 --conf "spark.sql.extensions=io.delta.sql.DeltaSparkSessionExtension" --conf
"spark.sql.catalog.spark_catalog=org.apache.spark.sql.delta.catalog.DeltaCatalog" app-spark-processing.py</i>

<h3>Crawlers</h3>
Usar os data catalog com o caminho o URI do objeto: s3://teste-872226808963/orders/
<br>
Sempre usar a barra no final para informar o data store.
<br>
Usar Natives tables

<h3>Athena</h3>
Importante: Alterar o engine para usar athena 3.0 (tabelas delta)
<br>
Utilize o arquivo querys.sql para consultar as tabelas diretamente no Athena.
