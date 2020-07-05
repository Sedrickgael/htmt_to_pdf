# htmt_to_pdf    in Django
Génération de reçu, ......


##  Installer  xhtml2pdf

```
pip install xhtml2pdf
```


##### Créer un fichier Render.py au niveau de votre application

```python
from io import BytesIO
from django.http import HttpResponse
from django.template.loader import get_template
import xhtml2pdf.pisa as pisa


class Render:

    @staticmethod
    def render(path: str, params: dict):
        template = get_template(path)
        html = template.render(params)
        response = BytesIO()
        pdf = pisa.pisaDocument(BytesIO(html.encode("UTF-8")), response)
        if not pdf.err:
            return HttpResponse(response.getvalue(), content_type='application/pdf')
        else:
            return HttpResponse("Error Rendering PDF", status=400)
```


## in views.py
```python
import datetime 
from . import Render
from . import models


def get(request):
        sales = models.YourModel.objects.all()  # Récupérer ce que vous voulez dans votre pdf
        today = datetime.datetime.now() # Avoir la date du jour
        params = {
            'today': today,
            'sales': sales,
        }
        return Render.Render.render('pdf.html', params) #Avoir un fichier pdf.html à la racine de votre dossier template
```


## in urls.py
```python
    from . import views
    path('render/pdf/', views.get, name="test"),
```
