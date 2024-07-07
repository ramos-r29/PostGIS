<h1>Subir um shapefile (shp) do IBGE para o PostgreSQL com PostGIS</h1>
<h2>Versão do S.O. utilizada: Debian 12.5</h2>
<h2>Versão do PostgreSQL utilizada: 16.3</h2>
<h2>Versão do PostGIS utilizada: 3.4</h2>

<br>

**01 - Certifique-se de ter instalado o PostgreSQL e PostGIS,  siga as instruções destes repositórios para instalação:**

- **Instalação do PostgreSQL:**
  
  https://github.com/ramos-r29/PostgreSQL/tree/main/01-PostgreSQL-install-debian
  
<br>

- **Instalação do PostGIS:**
  
  https://github.com/ramos-r29/PostGIS/blob/main/01-PostGIS-install/postgis_install_debian.md

<br>

**02 - Faça login no PostgreSQL:**
```shell
psql -U postgres -h localhost -p 5432
```

<br>

**03 - Crie um banco de dados e a extensão PostGIS neste banco:**
```sql
CREATE DATABASE db_geo;
```

- *Usando o client `psql` com o comando `\l` é possível listar os bancos dados existentes:*

<img src="https://github.com/ramos-r29/PostGIS/blob/main/02-Subir-shape-file-IBGE/imagens/list_db.png" alt="Listar DB">

- *Troque a conexão para o novo banco dados:*

```shell
\c db_geo
```
- *Crie a extensão PostGIS neste banco:*
```sql
CREATE EXTENSION postgis;
```

<img src="https://github.com/ramos-r29/PostGIS/blob/main/02-Subir-shape-file-IBGE/imagens/db_create.png" alt="Create DB">


<br>


**04 - Faça o download do arquivo `.zip` no site do IBGE, neste exemplo será utilizado o arquivo da malha municipal de 2022, mas pode ser aplicado a outros arquivos:**

- *Verifique se o `wget` esta instalado:*
```shell
which wget
```
<img src="https://github.com/ramos-r29/PostGIS/blob/main/02-Subir-shape-file-IBGE/imagens/verificar_wget.png" alt="wget">


- *Se `wget` estiver instalado a saída do comando `which` será algo similar a imagem acima, caso a saida seja vazia,  altualize os pacotes e instale o `wget` caso necessário:*

```shell
sudo apt-get update
```

```shell
sudo apt-get install -y wget
```

- *Faça o download do arquivo no site do IBGE:*

```shell
wget -P /home/rodrigo/Documents/shapefiles https://geoftp.ibge.gov.br/organizacao_do_territorio/malhas_territoriais/malhas_municipais/municipio_2022/Brasil/BR/BR_Municipios_2022.zip
```

*O parâmetro `-P` possibilita indicar o diretório onde deseja salvar o arquivo do download.*

<br>

**05 - É necessario descompactar o arquivo:**

- *Verifique se o `unzip` esta instalado:*

```shel
which unzip
```

<img src="https://github.com/ramos-r29/PostGIS/blob/main/02-Subir-shape-file-IBGE/imagens/verificar_unzip.png" alt="unzip">

- *Se o `unzip` estiver instalado a saída do comando `which` será algo similar a imagem acima, caso a saida seja vazia, atualize os pacotes e realize a instalação:*

```shell
sudo apt-get update
```
```shell
sudo apt-get install -y unzip
```

- *Descompacte o arquivo:*
```shell
unzip /home/rodrigo/Documents/shapefiles/BR_Municipios_2022.zip -d /home/rodrigo/Documents/shapefiles/malha_municipal_2022
```
*Neste comando o parâmero `-d` cria o diretório indicado caso ele não exista.*

<br>

**06 - Obter informação sobre a projeção utilizada no arquivo .shp (srid):**

*É possível obter dados sobe a projeção do `.shp` a partir do arquivo `.prj`:*

```shell
echo $(cat /home/rodrigo/Documents/shapefiles/malha_municipal_2022/BR_Municipios_2022.prj)
```

<img src="https://github.com/ramos-r29/PostGIS/blob/main/02-Subir-shape-file-IBGE/imagens/arquivo_prj.png" alt="cat prj">

*Foi utilizado o comando 'echo', pois, o arquivo `.prj` não costuma ter quebra de linha, e executando o `cat` com `echo` deixa visualmente melhor a saída.*

<br>

*Também pode-se obter esses dados com o pacote `gdal-bin`:*
- *Atualize os pacotes e instale o `gdal-bin`:*
```shell
apt-get update
```
```shell
apt-get install -y gdal-bin
```

- *Obtenha os dados da projeção:*
```shell
gdalsrsinfo /home/rodrigo/Documents/shapefiles/malha_municipal_2022/BR_Municipios_2022.shp
```
<img src="https://github.com/ramos-r29/PostGIS/blob/main/02-Subir-shape-file-IBGE/imagens/gdalsrsinfo.png" alt="gdalsrsinfo">

<br>

***Nesta caso a projeção esta em EPSG 4674.***

<br>

**07 - Gerar a DDL a partir do aquivo `.shp`:**

```shell
shp2pgsql -s 4674 -g geom -I /home/rodrigo/Documents/shapefiles/malha_municipal_2022/BR_Municipios_2022.shp tb_municipios_2022 > /home/rodrigo/Documents/shapefiles/ddl/ddl_municipios_2022.sql
```

*O comando `shp2pgsql` é usado para converter um arquivo shapefile (*.shp) em comandos SQL que podem ser executados em um banco de dados PostgreSQL com suporte a PostGIS. *

**Explicação dos Parâmetros:**

- **-s 4674:**
  
  *Especifica o sistema de coordenadas de entrada (SRS) do arquivo shapefile. Neste caso, 4674 é o código EPSG para o sistema de coordenadas SIRGAS 2000, conforme identificado anteriormente.*


- **-g geom:**
    
  *Define o nome da coluna que armazenará a geometria (geometria espacial) no banco de dados. No exemplo, geom é o nome da coluna onde serão armazenadas as informações de geometria espacial (pontos, linhas, polígonos, etc.).*

- **-I:**
  
  *Indica que o arquivo shapefile contém uma chave primária (primary key) que deve ser preservada durante a importação para o banco de dados. Isso é útil para garantir que não haja duplicatas ao inserir os dados no banco de dados.*

- **/home/rodrigo/Documents/shapefiles/malha_municipal_2022/BR_Municipios_2022.shp:**
  
  *Caminho completo para o arquivo shapefile que será convertido para SQL. Neste caso, é o caminho para o arquivo BR_Municipios_2022.shp localizado no diretório /home/rodrigo/Documents/shapefiles/malha_municipal_2022/.*

- **tb_municipios_2022:**
  
  *Nome da tabela que será criada no banco de dados PostgreSQL. Neste exemplo, a tabela será chamada tb_municipios_2022.*

- **> /home/rodrigo/Documents/shapefiles/ddl/ddl_municipios_2022.sql:**
  
  *Redireciona a saída dos comandos SQL gerados pelo shp2pgsql para o arquivo especificado. Neste caso, os comandos SQL serão escritos no arquivo /home/rodrigo/Documents/shapefiles/ddl/ddl_municipios_2022.sql.*

<br>

**08 - Carregue a tabela no banco de dados:**

```shell
psql -U postgres -h localhost -p 5432 -d db_geo < /home/rodrigo/Documents/shapefiles/ddl/ddl_municipios_2022.sql
```
<img src="https://github.com/ramos-r29/PostGIS/blob/main/02-Subir-shape-file-IBGE/imagens/table.png" alt="table">

*No client psql é possivel listar as tabelas da base com o comando `\d`:*







