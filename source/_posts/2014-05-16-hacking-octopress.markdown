---
layout: post
title: "Hacking Octopress"
date: 2014-05-16 23:12:02 -0400
comments: true
sharing: true
footer: true
categories: [Octopress,Ruby]
---
Octopress es un framework para páginas estáticas basado en Jekyll, sin lugar a duda es una excelente y divertida manera de publicar un blog o página personal. Como su eslogan lo dice "A blogging framework for hackers" necesita de ciertos conocimiento básicos de Ruby y su servidor Rack. Pero por sobre todas las cosas necesita que perdamos el miedo a la línea de comandos ya que todo el trabajo se debe hacer desde un editor de textos normal y comandos en la consola.

Octopress ofrece un template básico muy bien logrado que representa un gran avance sobre Jekyll, adicionalmente existen muchos templates de terceros que son muy fáciles de utilizar, sin embargo,  por el momento he decidido mantenerme con el template estándar y sólo hacer algunos ajustes básicos. De todos estos ajustes considero que vale la pena comentar sobre los siguientes:

##Cambio de color de background de los bloques de código

Por defecto Octopress utiliza Solarized el cual tiene 2 templates "dark" y "light", el primero es muy oscuro y el segundo es un color entre amarillo que personalmente no es de mi agrado. Para cambiar este color simplemente hay editar el archivo `/sass/custom/_colors.scss` y quitar el comentario a `$solarized` y a las variables `$base2` y `base3`, a las cuales podemos cambiar los valores deseados

{% codeblock /sass/custom/_colors.scss %}
$solarized: light;
$base2:             #dddddd;
$base3:             #f0f0f0;
{% endcodeblock %}

El folder `/sass/custom` contiene muchas sugerencias para el cambio de estilo en Octopress, tomen su tiempo y vaya experimentando. Tengan en cuenta que todo cambio  con CSS debe ser hecho en el archivo `sass/custom/_styles.scss` que es el último en cargarse.

##Traducción al español

Octopress no cuenta con soporte para [i18n](http://es.wikipedia.org/wiki/I18n) por lo que hay que hacer un trabajo manual de reemplazo de todas las etiquetas, los principales archivos a traducir se encuentran en los folders:

{% codeblock %}
/source/custom
/source/post
/source/_layout
{% endcodeblock %}

En cuanto a la traducción, el cambio mas complicado es el de las fechas, para lo cual use como modelo el siguiente código que encontré en [github](https://github.com/vigo/octopress)

Para realizar el cambio inicialmente debemos crear los arrays con los valores de los días y meses en español, esto los debemos adicionar el archivo `/plugins/date.rb`

{% codeblock /plugins/date.rb %}
module Octopress
  module Date

    # Days and Months in spanish
    MONTHNAMES_ES = [nil,
      "Enero", "Febrero", "Marzo", "Abril", "Mayo", "Junio",
      "Julio", "Agosto", "Septiembre", "Octubre", "Noviembre", "Diciembre"]
    ABBR_MONTHNAMES_ES = [nil,
      "Ene", "Feb", "Mar", "Abr", "May", "Jun",
      "Jul", "Ago", "Sep", "Oct", "Nov", "Dic"]
    DAYNAMES_ES = ["Domingo", "Lunes", "Martes", "Mi&eacute;rcoles",
      "Jueves", "Viernes", "S&aacute;bado"]
    ABBR_DAYNAMES_ES = ["Dom", "Lun", "Mar", "Mi&eacute;",
       "Jue", "Vie", "S&aacute;b"]
...       
{% endcodeblock %}

En el mismo archivo localizar la función `format_date`
{% codeblock /plugins/date.rb %}
...    
    def format_date(date, format)
      date = datetime(date)
      if format.nil? || format.empty? || format == "ordinal"
        date_formatted = ordinalize(date)
      else
        date_formatted = date.strftime(format)
        date_formatted.gsub!(/%o/, ordinal(date.strftime('%e').to_i))
      end
      date_formatted
    end
...
{% endcodeblock %}

Y reemplazarla por la siguiente función:
{% codeblock /plugins/date.rb %}
...    
    def format_date(date, format)
      date = datetime(date)
      if format.nil? || format.empty? || format == "ordinal"
        date_formatted = ordinalize(date)
      else
        date_formatted = format.gsub(/%a/, ABBR_DAYNAMES_ES[date.wday])
        date_formatted = date_formatted.gsub(/%A/, DAYNAMES_ES[date.wday])
        date_formatted = date_formatted.gsub(/%b/, ABBR_MONTHNAMES_ES[date.mon])
        date_formatted = date_formatted.gsub(/%B/, MONTHNAMES_ES[date.mon])
        date_formatted = date.strftime(date_formatted)
      end
      date_formatted
    end
...
{% endcodeblock %}

Después de este procedimiento todas las fechas de los posts estarán disponibles en español. Así mismo es posible cambiar el formato de presentación en el archivo `/_config.yml` por ejemplo utilizando la siguiente plantilla:

{% codeblock /_config.yml %}
date_format: "%d %B %Y"
{% endcodeblock %}

Esta implementación tiene una falencia que se hace evidente en el la página del Archivo de posts, por el momento mi solución es la presentar el mes en formato numérico cambiando las siguientes líneas en el archivo `/source/post/archive_post.html`

{% codeblock lang:html /source/post/archive_post.html %}
{% raw %}
...
<time datetime="{{ post.date | datetime | date_to_xmlschema }}" pubdate>{{ post.date | date: "<span class='month'>%m</span>/<span class='day'>%d</span> <span class='year'>%Y</span>"}}</time>
...
{% endraw %}
{% endcodeblock %}

