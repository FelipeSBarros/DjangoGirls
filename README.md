## Django girls Tutorial

```shell script
conda create -n djangogirls python django 
# conda env export > environments.yml
# conda env create -f environment.yml
```


### Criando projeto

```python
django-admin startproject mysite .

```

* `manage.py` é um script que ajuda com a gestão do site. Com ele, podemos iniciar um servidor de web no nosso computador sem instalar nada, entre outras coisas.  
* O arquivo `settings.py` contém a configuração do seu site.  
*Lembra de quando falamos sobre um carteiro verificando onde entregar uma carta? O arquivo `urls.py` contém uma lista dos padrões usados por urlresolver.  

#### Alterando configs (settings.py)

* [Definindo horário](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones): Buscar `TIME_ZONE` e alterar para a time_zone de interesse;  
* [Defininfo lingua/tradução](https://docs.djangoproject.com/en/2.0/ref/settings/#language-code): Buscar `LANGUAGE_CODE` e alterar para o que for conveniente.  
* Caminho a arquivos estáticos: Bucar ao fim do arquivo `STATIC_URL`, e adicionar uma variavel: `STATIC_ROOT = os.path.join(BASE_DIR, 'static')`  
* Allowed_host: `ALLOWED_HOSTS = ['127.0.0.1', '.pythonanywhere.com']`  
* Criando o banco de dados: `python manage.py migrate`  

#### Iniciando app  

`python manage.py startapp blog`  
Após criar a app, informar ao Django que a mesma deverá ser usada: em `INSTALLED_APPS`. adicionar `blog,`.  
Vamos criar o modelo do blog em: `blog/models.py`  

##### Modelos  

Modelo é um tipo de objeto, com uma estrutura bem definida., que é salvo num banco de dados.  

> :warning: Sempre inicie o nome de uma classe com uma letra em maiúsculo.
A regra para nomes de métodos é sempre usar letras minúsculas e no lugar dos espaços em branco, usar o caractere sublinhado (_). :warning:  

Criando as tabelas:
`python manage.py makemigrations blog`  
E executando as migrações:  
`python manage.py migrate blog`  

##### Admin para CRUD  
Vamos importar o modelo criado anteriormente (`blog/admin`) e vamos registra-lo na pagina de administração.  
Para cessar o admin: `127.0.0.1:8000/admin`  
Para criar superusuário: `python manage.py createsuperuser`

### Deploy  
Adicionar oa `.gitignore`:
```
*.pyc
*~
__pycache__
myvenv
db.sqlite3
/static
.DS_Store
```  
add todos: `git add --all .`