# Projeto-wordpress-AWS
O propósito do projeto foi criar uma vm básica (qualificada para o nível gratuito), utilizando a nuvem AWS.
  
  A VM (EC2) foi criada no tipo t2.micro (é uma instância de baixo custo e bom desempenho) na plataforma Ubuntu (é base de linux, fácil de trabalhar), utilizei a zona de disponibilidade us-east-1c tenho 8 GiB de tamanho do tipo gb2 (o projeto é leve, então o mínimo é o suficiente).
 
 O banco de dados mysql foi criado na versão 8.0.28 (porém tem a 8.0.30 como mais recente),classe db.t3.micro (banco de dados de acesso remoto), tendo 1 GB de RAM, um SSD de uso geral de 20 GiB tendo como máximo 1000 GiB (armazenamento mínimo do SSD)
 
 Antes de proseguir para o CMD e instalação, será necessário instalar dois programas:
 •puTTYgen
 •puTTY

  Antes de usar qualquer um deles, no serviço EC2 no painel da AWS, será necessário acessar o par de chaves e criar um. Para isso basta dar um nome (exemplo:kp1), selecionar a opção RSA (já que é compatível com diversos sistemas operacionais, já o ED25519 é compatível somente com linux e mac), e em seguida escolher o tipo de arquivo .ppk (que será utilizado no puTTY).

  Vale ressaltar que o puTTYgen só será necessário caso não gere o arquivo .ppk direto, neste caso será necessário pegar o IP privado da chave SSH e converter no arquivo no programa.

  Após a criação do arquivo .ppk ele será utilizado no puTTY da seguinte forma:
  >Na parte de Session pedirá o IP da instância (para checar basta clickar na máquina no painel da AWS,)

   >SSH

    >Auth

     >Após acessar a opção Auth, terá um campo pedindo "Private Key for authentication", basta clickar em browse e utilizar o arquivo .ppk anteriormente criado.
     

 Uma vez feito isso, estará conectado no terminal da máquina, para conectar ele irá pedir o usuário, que no caso será "ubuntu"

 Assim a VM estará conectada e pronta para instalar o wordpress, segue o script (explicado):

 #! /bin/bash

 apt update -> atualiza os programas

apt -y install apache2 -> instala apache2 (para quando colocar o IP, saber que a máquina está viva) 

apt -y install php -> instala o php, para o wordpress poder funcionar 

systemctl restart apache2 -> reinicia o apache 

cd /var/www/html -> abre a pasta /var/www/html o comando cd serve pra abrir diretórios 

rm index.html -> remove o arquivo index.html 

cd .. -> volta duas pastas

apt -y install wget -> instala wget wget http://wordpress.org/latest.tar.gz -> dá um wget no wordpress 

tar -xzvf latest.tar.gz -> descompacta o arquivo zipado 

apt -y install rsync -> instala rsync, serve para sincronizar diretórios 

rsync -avP wordpress/ /var/www/html -> sincroniza wordpress pra pasta /var/www/html 

cd /var/www/html -> abre a /var/www/html 

apt install php-mysql php-cgi php-cli php-gd -y -> instala todos esses programas

systemctl restart apache2 -> restarta o apache2 

chown -R www-data:www-data /var/www/html -> dá permissão ao var/www/html 

systemctl restart apache2 -> restarta o apache2
 
 Após o wordpress instalado na VM, só foi preciso conectar o banco de dados:
Mysql -h endpoint do banco -u usuário do database (ex: admin) -p
 
 Uma vez conectado o processo se torna mais simples, o comando SHOW DATABASES permite mostrar quais bancos ja estão conectados, mas caso necessite criar um novo utilizamos a linha:

CREATE DATABASE nome do banco; (ex: CREATE DATABASE worpress;)
 
 Um possível erro é a falta de permissão do usuário, e para resolvê-lo criei um novo usuário e dei todas as permissões para ele (levando em consideração que não tinha outros usuários envolvidos)

CREATE USER ‘username’@‘localhost’ IDENTIFIED BY ‘password’;

GRANT ALL PRIVILEGES ON [DATABASE name].* TO ‘new_user_name’@’localhost’;

Depois de todo o processo, basta colocar o DNS público da VM na barra de pesquisa do navegador e configurar o wordpress.