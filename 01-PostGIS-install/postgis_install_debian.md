<h1>Instalação do PostGIS</h1>
<h2>Versão do S.O. utilizada: Debian 12.5</h2>
<h2>Versão do PostgreSQL utilizada: 16.3</h2>
<h2>Versão do PostGIS utilizada: 3.4</h2>

**1 - Verifique se o PostgreSQL esta instalado:**

`dpkg -l | grep postgresql`


<img src="https://github.com/ramos-r29/PostGIS/blob/main/01-PostGIS-install/imagens/dpkg_postgresql.png" alt="Saida do comando dpkg">

**2 - Caso o PostgreSQL não esteja instalado siga a instalação indicada no repositório do link abaixo:**

https://github.com/ramos-r29/PostgreSQL/tree/main/01-PostgreSQL-install-debian

**3 - Verifique se extensão está disponível:**

`apt search postgresql-16 | grep postgis`

<img src="https://github.com/ramos-r29/PostGIS/blob/main/01-PostGIS-install/imagens/search.png" alt="Saida do comando search">

**4 - Instale os pacotes do PostGIS:**

`sudo apt-get install -y postgis`

**5 - Atualize os pacotes:**

`sudo apt-get -y update`

**6 - Confirme a instalação do PostGIS:**

`dpkg -l | grep postgis`

<img src="https://github.com/ramos-r29/PostGIS/blob/main/01-PostGIS-install/imagens/dpkg_postgis.png" alt="Saida do comando dpkg">

**7 - Faça login no PostgreSQL a partir de um client como o _psql_, por exemplo, e crie a extensão postgis:**

`psql -U postgres -h localhost -p 5432`

`SELECT * FROM pg_extension;`

`CREATE EXTENSION postgis;`

<img src="https://github.com/ramos-r29/PostGIS/blob/main/01-PostGIS-install/imagens/create.png" alt="Saida do comando create">

**8 - Verifique se extensão foi criada:**

`SELECT  oid,  extname, extowner, extnamespace, extrelocatable, extversion, extconfig FROM pg_extension;`


<img src="https://github.com/ramos-r29/PostGIS/blob/main/01-PostGIS-install/imagens/select.png" alt="Saida do comando select">

<h6>Pronto, o PostGIS esta instado e pronto para uso !!</h6>

**Fonte:**

https://postgis.net/documentation/getting_started/install_ubuntu/

https://trac.osgeo.org/postgis/wiki/UsersWikiPostGIS3UbuntuPGSQLApt
