Estudos pra não enferrujar e também pra aprender na prática como mexer em alguns recursos da AWS *-*



Bootcamp de Engenharia de Dados
Setup AWS
Crie uma conta na AWS (Conta root);
Crie uma conta com permissão de administrador para ser a conta usada para projeto;
Crie um servidor PostgreSQL na versão 13 usando o Amazon RDS;
Clique no Security Groupo e libere o acesso a porta 5432 vindo do seu ip público;
Setup Ingestion to RDS
Edite o arquivo .env com as informções do Amazon RDS;
Instale todas as bibliotecas necessárias para rodar a aplicação de ingestão com o comando:
pip install -r requirements.txt
Execute a aplicação app-ingestion-sql
Conecte no banco de dados usando algum cliente de banco de dados, por exemplo o dbeaver e verifique se as tabelas foram criadas.
Setup Ingestion to Data Lake
Edite o arquivo .env informando as credencias de chave de acesso, por exemplo:
AWS_ACCESS_KEY_ID=AKIA4WFGE4SB25sdfadf
AWS_SECRET_ACCESS_KEY=mwXXs8NjLwI83fnxrZ7rm4zsQHEftY3adfafd
Crie um bucket na aws, no meu caso criei raw-bootcampde-872226808963
Edit o arquivo .env informando o nome do bucket criado na variável BUCKET_NAME
Execute a aplicação app-mobile-customers.py
Verifique se os arquivos .json estão sendo inseridos no bucket.
Importante:

Interrompa o banco de dados RDS.

DMS Setup

Crie uma instância de replicação.
Crie os endpoints target e source.
Use o código abaixo para criar a police/role para o endpoint target no s3.
Documentação do s3 como target endpoint: https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Target.S3.html
{
    "Version": "2012-10-17",
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
}
Adicione a opção AddColumName no endpoint target.
Crie uma task para iniciar o DMS.
Setup EMR
Libere o acesso na porta 22. Para isso clique em EC2 security group clique no security group e libere a porta 22 para o IP.
Acesso via ssh com Putty:

Download PuTTY.exe to your computer from: https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html
Start PuTTY.
In the Category list, choose Session.
In the Host Name field, enter hadoop@ec2-52-23-204-207.compute-1.amazonaws.com
In the Category list, expand Connection > SSH and then choose Auth.
For private key file for authentication, choose Browse and select the private key file (key-boot02.ppk) that you used to launch the cluster.
Select Open.
Select Yes to dismiss the security alert.
Copie a aplicação para o servidor do Amazon EMR usando o WinScp (Windows) ou o scp (linux), para linux segue um exemplo:
scp -i ~/Downloads/pair-bootcamp.pem job-spark-app-emr-redshift.py hadoop@ec2-54-90-3-194.compute-1.amazonaws.com:/home/hadoop/
Para executar o cluster Spark use o comando abaixo:
spark-submit --packages io.delta:delta-core_2.12:2.0.0 --conf "spark.sql.extensions=io.delta.sql.DeltaSparkSessionExtension" --conf "spark.sql.catalog.spark_catalog=org.apache.spark.sql.delta.catalog.DeltaCatalog" app-spark-processing.py
Crawlers
Usar os data catalog com o caminho o URI do objeto: s3://teste-872226808963/orders/
Sempre usar a barra no final para informar o data store.
Usar Natives tables
Athena

Importante: Alterar o engine para usar athena 3.0 (tabelas delta)
Utilize o arquivo querys.sql para consultar as tabelas diretamente no Athena.