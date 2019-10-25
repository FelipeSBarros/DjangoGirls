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
Vamos importar o modelo criado anteriormente (`blog/admin`) e vamos registra-lo na pagina de administração com `admin.site.register(Post)`  
Para cessar o admin: `127.0.0.1:8000/admin`  
Para criar superusuário e pode acessá-lo: `python manage.py createsuperuser`  

### Deploy  
[Checklist de segunrança do DJango;](https://docs.djangoproject.com/en/2.0/howto/deployment/checklist/)  
Adicionar ao `.gitignore`:
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

**PythonAnywhere**:  
> Depois de se registrar no PythonAnywhere, você será levada ao seu painel de controle. Encontre o link para a página de "Account" (conta, em português) próximo ao topo no lado direito:
> em seguida, selecione a guia chamada "API Token" e aperte o botão que diz "Create new API token" (criar novo token API").  

Ir ao Bash, e instalar ferramenta pythonanywhere:
`pip3.6 install --user pythonanywhere`  
Agora vamos executar a ferramenta para configurar a nossa aplicação a partir do GitHub automaticamente:
`pa_autoconfigure_django.py https://github.com/<your-github-username>/my-first-blog.git` (use --nuke) se já tiver usado webapp...  


* Baixando o seu código do GitHub;  
* Criando um virtualenv no PythonAnywhere, igual ao que existe no seu computador  
* Atualizando o seu arquivo de configuração com algumas configurações sobre o deploy;  
* Criando um banco de dados no PythonAnywhere usando o comando manage.py migrate;  
* Criando os seus arquivos estáticos (nós aprenderemos sobre eles mais tarde)  
* E configurando o PythonAnywhere para servir a sua web app através da sua API.  

Banco de dados separados: Desenvolvimento e deploy. por isso, configurar de novo superuser:
`python manage.py createsuperuser`  

### URLs  
> Em Django, usamos algo chamado URLconf (configuração de URLs). URLconf é um conjunto de padrões que o Django vai usar para comparar com a URL recebida para encontrar a resposta correta.

A gestão de URLs são fetas pelo aquivo `urls.py` do **projeto** (não da app, aliás. app nem tem esse arquivo).  
> Isso significa que para cada URL que começa com admin/, o Django irá encontrar uma view correspondente.

Vamos fazer que o endereço http://127.0.0.1:8000 seja a pagina inicial do blog, exibindo lista de publicações.  

Adicionamos `path('', include('blog.urls'))` ao `urls.py` do projeto.  
:warning: Não deixe de fazer o import :warning:  
:warning: Estamos direcionando as requisições para o `blog.urls.py` que **não existe**. Vamos criá-lo. :warning:  

* Criar arquivo `urls.py` na pasta *blog* (ou app) e inserir:  
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.post_list, name='post_list'),
]
```   

> Aqui, estamos importando do Django a função url e todas as nossas views do aplicativo blog. E inserindo um primeiro padrão url,

:warning: (**Não temos nenhuma view** ainda, mas chegaremos a isso em um minuto!) :warning:  

No padrão url criado, estamos atribuindo uma view chamada `post_list` à URL raiz. Este padrão dirá ao Django que `views.post_list` é o lugar correto aonde ir se alguém entra em seu site pelo endereço 'http://127.0.0.1:8000 /'.  

* `name='post_list'` é o nome da URL que será usada para identificar a `view`. Podemos usar o mesmo nome ou outro diferente.  
:warning:  devemos fazer com que os nomes das URLs sejam únicos e fáceis de lembrar. :warning:  

### Views  

> Uma view é o lugar onde nós colocamos a "lógica" da nossa aplicação. Ela vai extrair informações do model que você criou e entregá-las a um template. Nós vamos criar um template no próximo capítulo. Views são apenas funções Python um pouco mais complicadas

Adicionar à `views.py` da pasta blog (ou app): 
```python
def post_list(request):
    return render(request, 'blog/post_list.html', {})
```  

### Templates  
Templates são guardados na pasta `templates` dentro do diretpório da sua app. Dentro dele criar uma nova pasta chamada "blog".
Final:  
```python
blog
└───templates
    └───blog
```  
**Pq criar outra pasta com nome blog** :question:
>(Você deve estar se perguntando porque nós precisamos de dois diretórios chamados blog - como você descobrirá mais para frente, essa é uma convenção que facilita a nossa vida quando as coisas começam a ficar mais complicadas.)

Criamos o arquivo html chamado `post_list.html`. E o deixamos em branco... Vemos como está o servidor:
>Se você ainda tem um erro TemplateDoesNotExist, tente reiniciar o seu servidor. Entre na linha de comando, interrompa o servidor pressionando Ctrl+C (Control seguido da tecla C, juntas) e reinicie-o rodando python manage.py runserver.

Sobre HTML:  
* head é um elemento que contém informações sobre o documento que não são mostradas na tela.  
* body é um elemento que contém tudo o que é exibido como parte de uma página da web.  

Elementos básicos:

* \<h1>Um título</h1> para o título da seção principal exibido na página
* \<h2>Um sub-título</h2> para um título um nível abaixo
* \<h3>Um sub-sub-título</h3> ... e por aí vai, até <h6>
* \<p>Um parágrafo de texto</p>
* \<em>texto</em> enfatiza seu texto
* \<strong>text</strong> enfatiza fortemente seu texto
* \<br> quebra a linha (você não pode digitar nada dentro da tag br e ela não possui uma tag de fechamento correspondente)
* \<a href="https://djangogirls.org">link</a> cria um link
* \<ul>\<li>primeiro item\</li>  
\<li>segundo item\</li>\</ul>  
* \<div>\</div> define uma seção da página

## Atualizando deploy:  

```python
cd ~/<your-pythonanywhere-domain>.pythonanywhere.com
git pull
```  

### QuerySet e ORM  
>QuerySet (conjunto de busca) é, em essência, uma lista de objetos de um dado modelo. QuerySet permite que você leia os dados a partir de uma base de dados, filtre e ordene.  

Exemplo usando o terminal django
```python
python manage.py shell

from blog.models import Post
Post.objects.all()
```

**Criando post pelo console django**  
```python
from django.contrib.auth.models import User
me = User.objects.get(username='felipe')
Post.objects.create(author=me, title='Teste console ', text='teste criacao post pelo console')
Post.objects.all()
```  

**Filtro**  

* `Post.objects.filter(author=me)`
* `Post.objects.filter(title__contains='Teste')` :warning: **Não é case sensitive**  
> :warning: dois caracteres de sublinhado (_) entre title e contains. O ORM do Django utiliza esta sintaxe para separar nomes de campo ("title") e operações ou filtros (como "contains")  

:warning: com `Post.objects.all()`, todos os post serão resgatados. Inclusive os não publicados.  
**Para filtrar os posts publicados, podemos usar o campo `published_date`. Para tal, precisaremos importar `timezone`:
```python
from django.utils import timezone
Post.objects.filter(published_date__lte=timezone.now())
```  

```python

post = Post.objects.get(title="Teste console ")
post.publish()
Post.objects.filter(published_date__lte=timezone.now())
```  

**Ordenando**  
Para ordenar, usamos o método `order_by('campo')`. ex:
`Post.objects.order_by('created_date')`  
:warning: para inverter a ordem, basta iserir `-` como prefixo do campo:  
`Post.objects.order_by('-created_date')`  

**Consultas Complexas com Encadeamento de Métodos**  
Um QuerySet pode ser ser invocados num outro QuerySet, o que resultará num novo QuerySet. Dessa forma, você pode combinar seus efeitos encadeando-los juntos:  
```python
Post.objects.filter(published_date__lte=timezone.now()).order_by('published_date')
```  

#### Atualizando view  
Papel das views: conectar modelos e templates.   
Vamos precisar pegar os modelos que queremos exibir e passá-los para o template na nossa lista de postagens post_list view. Em uma view, nós decidimos o que (qual modelo) será exibido em um template.  

Vamos adicionar a QuerySet na view, mais específicamente na função `post_list`, adicionando o resultado ao dicionário final do `render`.  
:warning: falta adiconar ao template a QuerySet  

### Tags de template Django  
> nos permitem transformar código similar a Python em código HTML para que você possa construir sites dinâmicos mais rápido e mais facilmente.  

:warning: Pra mostrar uma variável em um template do Django, usamos chaves duplas com o nome da variável;  
O Ajdnago entende essa variável como uma lista de objetos.  Por isso, usaremos um `for` para iterar sobre a lista e apresenta-las:  
```html
{% for post in posts %}
    {{ post }}
{% endfor %}
```

Misturando tag HTML com tag de template:
```html
{% for post in posts %}
    <div>
        <p>published: {{ post.published_date }}</p>
        <h2><a href="">{{ post.title }}</a></h2>
        <p>{{ post.text|linebreaksbr }}</p>
    </div>
{% endfor %}
```  

>Você notou que dessa vez nós usamos uma notação um pouco diferente ({{ post.title }} ou {{ post.text }})? Estamos acessando os dados em cada um dos campos que definimos no modelo do Post. Além disso, |linebreaks está passando o texto do post por um filtro, convertendo quebras de linha em parágrafos.  

### CSS  
Cascading Style Sheets (CSS - Folhas de Estilo em Cascata, em português);  
Em um arquivo CSS, nós determinamos estilos para elementos do arquivo HTML.    
Vamos usar o [Bootstrap](https://getbootstrap.com/)    

Add na `<head>` do html:
```html
<link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css">
<link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap-theme.min.css">
```  

**Arquivos Estáticos**  
* são todos os seus CSS e imagens. Seu conteúdo não depende do contexto de requisição e será o mesmo para todos os usuários.  
Vamos adicionar alguns arquivos estáticos à nossa app'`blog`, uma vez que o Django já sabe onde encontrá-lo.  

Criar novos diretórios:
```
djangogirls
└─── blog
     └─── static
          └─── css
               └─── blog.css
```  

**Vamos mudar a cor do nosso cabeçalho:**  
Adicionar ao blog.css:  
```
h1 a, h2 a {
     color: #C25100; 
}
```  

h1 a é um seletor CSS. Isto significa que estamos aplicando nossos estilos a qualquer elemento um dentro de um elemento h1; o seletor h2 a faz a mesma coisa com elementos h2. Então quando tivermos algo como um \<h1>\<a href="">link\</a>\</h1>, o estilo h1 a será aplicado.  
Você pode se lembrar desses nomes porque são a mesma coisa que as tags da seção HTML. a, h1 e body são exemplos de nomes de elementos. Também identificamos elementos pelo atributo class ou pelo atributo id. Class e id são nomes que você mesma dá ao elemento. Classes definem grupos de elementos e ids apontam para elementos específicos. Por exemplo, a tag a seguir pode ser identificada usando a tag de nome a, a classe external_link ou o id de link_to_wiki_page  

:warning: Nós também precisamos dizer ao nosso template HTML que adicionamos algum CSS  
Na primeira linha do HTML adicionar `{% load static %}` e entre as linhas `head`: `<link rel="stylesheet" href="{% static 'css/blog.css' %}">`  
  
Dando uma margem um pouco maior ao lado esquerdo da nossa pagina.  
```html
body {
    padding-left: 15px;
}
```  

Alterando a fonte. Add no css::  
`<link href="//fonts.googleapis.com/css?family=Lobster&subset=latin,latin-ext" rel="stylesheet" type="text/css">`

:warning: Agora, adicionaremos blocos de declaração a seletores diferentes. Seletores começando com . se referem às classes.  

### Estendendo os templates  
Template extending - extensão de templates: Significa que você pode usar as mesmas partes do seu HTML em diferentes páginas do seu site.  

**template base**  
```html
blog
└───templates
    └───blog
            base.html
            post_list.html
```  

:warning: extendemos o template `post_list.html` que herda o `base.html`  

### Ampliando aplicação  
**Criando link para detalhes do post**  
`<h2><a href=" {% url 'post_detail' pk=post.pk %}">{{ post.title }}</a></h2>`  
Estamos usando as tags de template do Django. Dessa vez, usamos uma que cria uma URL para nós! A parte post_detail significa que o Django espera uma URL no arquivo blog/urls.py com o nome definido como name='post_detail'  
Acessamos a chave primária escrevendo post.pk, do mesmo modo que podemos acessar outros campos (title, author, etc.) no nosso objeto de Post!  
Agora temos que criar uma viw `post_details`  

**Criando uma url**  
A ideia é que seja apresentada em: `http://127.0.0.1:8000/post/1/`  

A parte post/<int:pk>/ especifica um padrão de URL – vamos explicar:
* `post/` significa apenas que a URL deve começar com a palavra post seguida por /. Até aqui, tudo bem.  
* `<int:pk>` – essa parte é um pouco mais complicada. Ela nos diz que o Django espera um objeto do tipo inteiro e que vai transferi-lo para a view como uma variável chamada pk.  
* / – por fim, precisamos adicionar uma / ao final da nossa URL.  

>Isso significa que se você digitar http://127.0.0.1:8000/post/5/ em seu navegador, o Django vai entender que você está procurando uma view chamada post_detail e vai transferir a informação de que pk é igual a 5 para essa view.  

**Adicionando a view de detalhes do post**  
Em views:  
```python
from django.shortcuts import render, get_object_or_404
...
def post_detail(request, pk):
    post = get_object_or_404(Post, pk=pk)
    return render(request, 'blog/post_detail.html', {'post': post})

```

**Criando um template para os detalhes do post**  
```html
{% extends 'blog/base.html' %}

{% block content %}
    <div class="post">
        {% if post.published_date %}
            <div class="date">
                {{ post.published_date }}
            </div>
        {% endif %}
        <h2>{{ post.title }}</h2>
        <p>{{ post.text|linebreaksbr }}</p>
    </div>
{% endblock %}
```  

### Atualizando o deploy  
> :warning: sempre que alteramos nossos arquivos CSS, precisamos rodar um comando extra no servidor para dizer a ele que os atualize. O comando se chama collectstatic.

>Comece ativando seu virtualenv, se ele já não estiver ativo (para isso, o PythonAnywhere usa um comando chamado workon que é bem parecido com o comando source myenv/bin/activate que vosê usa no seu computador):

### Formulários Django  
> Com formulários, temos poder absoluto sobre nossa interface - podemos fazer quase tudo que pudermos imaginar!  

Vamos criar um ModelForm que salva o resultado do formulário em um determinado modelo. Nesta caso, nosso nodelo de post.  
:warning: forms têm seu próprio arquivo: `forms.py`  
```
blog
   └── forms.py
```  
*  precisamos importar o módulo de formulários do Django  
* Precisamos dizer ao Django que esse form é um ModelForm (pro Django fazer algumas mágicas para nós) – forms.ModelForm é o responsável por essa parte.  
* Em seguida, temos a class Meta em que dizemos ao Django qual modelo deverá ser usado para criar este formulário (model = Post)  
* Por fim, podemos dizer quais campos devem entrar no nosso formulário. Neste cenário, queremos que apenas o title e o text sejam expostos -- author deve ser a pessoa que está logada no sistema (nesse caso, você!) e created_date deve ser configurado automaticamente quando criamos um post (no código)  

:warning: Tudo o que precisamos fazer agora é usar o formulário em uma view e mostrá-lo em um template. Mas antes: criaremos um link para a página, uma URL, uma view e um template.  


**Link para a página com o formulário**  
Adicionaremos à template `base.html`:
`<a href="{% url 'post_new' %}" class="top-menu"><span class="glyphicon glyphicon-plus"></span></a>`  
Note que queremos chamar nossa nova view de post_new. A classe "glyphicon glyphicon-plus" é fornecida pelo tema (bootstrap) que estamos usando, e nos mostrará um sinal de mais.  


**URL**  
Em `blog/urls.py`: 
`path('post/new/', views.post_new, name='post_new'),`  

**Views**  
Em `blog/views.py `:
```html
from .forms import PostForm
...
def post_new(request):
    form = PostForm()
    return render(request, 'blog/post_edit.html', {'form': form})  
```  
>Para criar um novo formulario Post, devemos chamar PostForm() e passá-lo para o template. Voltaremos a esta view depois, mas por enquanto, vamos criar um template para o formulário.  

**Template**  
> Precisamos criar um arquivo post_edit.html na pasta blog/templates/blog. Para fazer o formulário funcionar, precisamos de várias coisas:  
>* Temos que exibir o formulário. Podemos fazer isso com (por exemplo) {{ form.as_p }}.  
>* A linha acima precisa estar dentro de uma tag HTML form: <form method="POST">...</form>.  
>* Precisamos de um botão Salvar. Fazemos isso com um botão HTML: <button type="submit">Save</button>.  
>* E finalmente, depois de abrir a tag <form ...>, precisamos adicionar {% csrf_token %}. 
>:warning: Isso é muito importante, pois é isso que torna o nosso formulário seguro! Se você esquecer esta parte, o Django vai reclamar quando você tentar salvar o formulário.  

**Salvando o formulário**  
> Lembra que no arquivo HTML, nossa definição de form incluiu a variável method="POST"? Todos os campos vindos do "form" estarão disponíveis agora em request.POST  

>Em nossa view temos duas situações diferentes com as quais lidar: primeiro, quando acessamos a página pela primeira vez e queremos um formulário em branco e segundo, quando voltamos para a view com todos os dados do formulário que acabamos de digitar. 

Em `blog/views.py`:
```python
def post_new(request):
     if request.method == "POST":
         form = PostForm(request.POST)
         if form.is_valid():
             post = form.save(commit=False)
             post.author = request.user
             post.published_date = timezone.now()
             post.save()
             return redirect('post_detail', pk=post.pk)
     else:
         form = PostForm()
     return render(request, 'blog/post_edit.html', {'form': form})
```  
>:warning: checa se o formulário está correto (todos os campos requeridos estão prontos e valores incorretos não serão salvos)  

>:warning: commit=False significa que não queremos salvar o modelo de Post ainda.  

>:warning: `post.save()` vai preservar as alterações (adicionando o autor) e é criado um novo post no blog!  

>:warning: Adicionando `return redirect('post_detail', pk=post.pk)` para redirecionar após salvar o post.    

### Editando Formulário  
> Vamos criar algumas coisas importantes rapidinho:

em `blog/templates/blog/post_detail.html`, após o `{% endif %}:
`<a class="btn btn-default" href="{ $ url 'post_edit' pk=post.pk %}"><span class="glyphicon glyphicon-pencil"></span></a>`

em `blog/urls.py`:  
`path('post/<int:pk>/edit/', views.post_edit, name='post_edit'),`  

O que falta é uma view:  
```python
def post_edit(request, pk):
...
post = get_object_or_404(Post, pk=pk) 
...
form = PostForm(request.POST, instance=post)
...
form = PostForm(instance=post)
```
Bem parecido com o a view `post_view`, com alguns detalhes:
1. quando criamos um formulário, passamos este post como uma instância tanto quando salvamos o formulário;  
1. como quando apenas abrimos um formulário para editar esse post  

### Segurança  
> Vamos fazer com que o botão de criar post apareça para quem está logado e para mais ninguém.  

Em blog/templates/blog/base.html, procure nossa div page-header e a tag de link que você colocou mais cedo.  
Incluir: `{% if %}`:
```python
{% if user.is_authenticated %}
    <a href="{% url 'post_new' %}" class="top-menu"><span class="glyphicon glyphicon-plus"></span></a>
{% endif %}

```

:warning: Este {% if %} fará com que o link seja enviado ao navegador se o usuário que requisitou a página estiver logado. Isso não protege o blog completamente da criação de um novo post, mas é um bom começo.  

Faremos o mesmo para o icone de editar.