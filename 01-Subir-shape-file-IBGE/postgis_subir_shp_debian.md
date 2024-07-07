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
