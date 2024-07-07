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

**02 - Faça login no PostgreSQL**
```shell
psql -U postgres -h localhost -p 5432
```

<br>

**03 - Crie um banco de dados**
```sql
CREATE DATABASE db_geo;
```
<br>

**04 - Usando o client `psql` com o comando `\l` é possível listar os bancos dados existentes:**

<img src="https://github.com/ramos-r29/PostGIS/blob/main/02-Subir-shape-file-IBGE/imagens/list_db.png" alt="Listar DB">

<br>

**5 - Faça o download do arquivo `.shp` no site do IBGE, neste exemplo será utilizado o arquivo da malha municipal de 2022, mas pode ser aplicado a outros arquivos conforme precisar**

```shell
wget -P /home/rodrigo/Documents/shapefiles https://geoftp.ibge.gov.br/organizacao_do_territorio/malhas_territoriais/malhas_municipais/municipio_2022/Brasil/BR/BR_Municipios_2022.zip
```

*O parâmetro `-P` possibilita indicar o diretório onde deseja salvar o arquivo do download.*

<br>

**6 - É necessario descompactar o arquivo:**

- *Verifique se o unzip esta instaldo*

```shel
  which unzip
```




