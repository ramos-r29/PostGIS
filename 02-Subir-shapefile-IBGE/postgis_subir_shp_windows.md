<h1>Subir um shapefile (shp) do IBGE para o PostgreSQL com PostGIS</h1>
<h2>Versão do S.O. utilizada: Windows 11</h2>
<h2>Versão do PostgreSQL utilizada: 16.3</h2>
<h2>Versão do PostGIS utilizada: 3.4</h2>

<br>

**01 - Certifique-se de ter instalado o PostgreSQL e PostGIS,  siga as instruções destes repositórios para instalação:**

- **Instalação:**
  
  https://github.com/ramos-r29/PostGIS/blob/main/01-PostGIS-install/postgis_install_windows.md

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

- **Usando o client `psql` com o comando `\l` é possível listar os bancos dados existentes:**

- **Troque a conexão para o novo banco dados:**

```shell
\c db_geo
```
- **Crie a extensão PostGIS neste banco:**
  
```sql
CREATE EXTENSION postgis;
```
<br>



**04 - Faça o download do arquivo `.zip` no site do IBGE, neste exemplo será utilizado o arquivo da malha municipal de 2022, mas pode ser aplicado a outros arquivos:**

```shell
curl -o BR_Municipios_2022.zip https://geoftp.ibge.gov.br/organizacao_do_territorio/malhas_territoriais/malhas_municipais/municipio_2022/Brasil/BR/BR_Municipios_2022.zip
```

<br>

**05 - É necessario descompactar o arquivo:**

```shell
tar -xf BR_Municipios_2022.zip
```

<br>

**06 - Obter informação sobre a projeção utilizada no arquivo .shp (srid):**

**É possível obter dados sobe a projeção do `.shp` a partir do arquivo `.prj`:**

```shell
more BR_Municipios_2022.prj
```

**Neste caso a projeção esta em EPSG 4674.**

<br>

**07 - Gerar a DDL a partir do aquivo `.shp`:**

```shell
cd C:\Program Files\PostgreSQL\16\bin
```

```shell
shp2pgsql -s 4674 -g geom -I C:\Users\rodrigo\BR_Municipios_2022\BR_Municipios_2022.shp tb_municipios_2022 > C:\Users\rodrigo\BR_Municipios_2022\ddl_municipios_2022.sql
```

**O comando `shp2pgsql` é usado para converter um arquivo shapefile (*.shp) em comandos SQL que podem ser executados em um banco de dados PostgreSQL com suporte a PostGIS.**

**Explicação dos Parâmetros:**

- **-s 4674:**
  
  **Especifica o sistema de coordenadas de entrada (SRS) do arquivo shapefile. Neste caso, 4674 é o código EPSG para o sistema de coordenadas SIRGAS 2000, conforme identificado anteriormente.**


- **-g geom:**
    
  **Define o nome da coluna que armazenará a geometria (geometria espacial) no banco de dados. No exemplo, geom é o nome da coluna onde serão armazenadas as informações de geometria espacial (pontos, linhas, polígonos, etc.).**

- **-I:**
  
  **Indica que o arquivo shapefile contém uma chave primária (primary key) que deve ser preservada durante a importação para o banco de dados. Isso é útil para garantir que não haja duplicatas ao inserir os dados no banco de dados.**

- **C:\Users\rodrigo\BR_Municipios_2022\BR_Municipios_2022.shp:**
  
  **Caminho completo para o arquivo shapefile que será convertido para SQL. Neste caso, é o caminho para o arquivo BR_Municipios_2022.shp localizado no diretório C:\Users\rodrigo\BR_Municipios_2022\BR_Municipios_2022.shp.**

- **tb_municipios_2022:**
  
  **Nome da tabela que será criada no banco de dados PostgreSQL. Neste exemplo, a tabela será chamada tb_municipios_2022.**

- **> C:\Users\rodrigo\BR_Municipios_2022\ddl_municipios_2022.sql:**
  
  **Redireciona a saída dos comandos SQL gerados pelo shp2pgsql para o arquivo especificado. Neste caso, os comandos SQL serão escritos no arquivo C:\Users\rodrigo\BR_Municipios_2022\ddl_municipios_2022.sql.**

<br>

**08 - Carregue a tabela no banco de dados:**

```shell
psql -U postgres -h localhost -p 5432 -d db_geo < 'C:/Users/rodrigo/BR_Municipios_2022/ddl_municipios_2022.sql'
```


**No client psql é possivel listar as tabelas da base com o comando `\d`:**


#### Pronto, o arquivo esta carregado no banco de dados e pode ser integrado com sofwares SIG's como o QGIS por exemplo.
