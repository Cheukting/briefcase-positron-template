# Let's build a BeeWare app that uses Django

In this workshop, we will go through the basics of building a BeeWare app. However, instead of starting from scratch, we will assume that we have a Django app that is already built - feel free to bring your own, I will use the [first step tutorial on Django documentation](https://docs.djangoproject.com/en/4.2/intro/tutorial01/).

Then, we will start building the BeeWare project and link it up with the already working Django app. After building our example, we will also discover other alternatives that BeeWare can work with Django so in the future, if you are building another BeeWare app, you can decide which method you want to use.

## Before coming to the workshop
- make sure you have set up a virtual environment ([see here](https://docs.beeware.org/en/latest/tutorial/tutorial-0.html))
- download [Xcode](https://apps.apple.com/au/app/xcode/id497799835?mt=12) if you want to build an iOS app
- bring your own Django app, or use the one on [Django tutorial](https://docs.djangoproject.com/en/4.2/intro/tutorial01/) - we need the code ready to use

---

## Introduction of BeeWare

BeeWare is a toolkit that enable users to create native looking app using only Python. It's philosophy is "Write once. Deploy everywhere." where your app can be deployed on iOS, Android, Windows, MacOS, Linux, Web, and more. Inside this toolkit, you will find:

- Briefcase: A Python library that convert a project into a standalone native application.
- Cricket: it's test runner GUI
- Toga: A Python native, OS native GUI toolkit

In this workshop, we will focus on using briefcase to deploy our app. With Toga, we can hook up our Django web app using positron.

## Install the packages

Assuming that you have [created and inside a virtual environment]((https://docs.beeware.org/en/latest/tutorial/tutorial-0.html)). We will install `briefcase`, `toga`, `cookiecutter` and `Django`:

```
$ python -m pip install briefcase toga cookiecutter Django
```

## Creating the project

Normally, if you follow the [official tutorial](https://docs.beeware.org/en/latest/tutorial/tutorial-1.html), we will create a new project using the `briefcase new` command. With that command, briefcase will generate a new project using the default template using it's CLI. However, since we want to use the positron Django template, we will use cookiecutter to create our new project:

```
$ cookiecutter https://github.com/Cheukting/briefcase-positron-template.git
```

[This template](https://github.com/Cheukting/briefcase-positron-template) is a fork of the [original default briefcase template](https://github.com/beeware/briefcase-template). The difference is, we will have new positron options (option 6 & 7) which will come with the positron code and the webapp folders.

Once you hit enter, you will be asked for various options. Here are the recommended options for this workshop:

- formal_name - `Positron`

- app_name - Accept the default `positron`

- class_name - Accept the default `positron`

- module_name - Accept the default `positron`

- project_name - `Positron`

- description - Accept the default value (or enter your own)

- author - Your name

- author_email - Your email address. This will be used when submitting the app to an app store.

- bundle - Accept the default bundle (com.example)

- url - Accept the default URL (https://example.com/helloworld). It will only be used if you publish your application to an app store. We do not need a real one for now.

- license - Accept the default license (BSD) (or choose your own)

- gui_framework - Choose 6: PositronDjango

- test_framework - Accept the default `pytest`

- briefcase_version - Accept the default (don't worry about it now)

- template - Accept the default (don't worry about it now)

- branch - Accept the default (don't worry about it now)

After inputing all the values, a project folder `positron/` will be generate. Go to that directory:

```
$ cd positron/
```

Look into `positron/src/positron/app.py` you will see the positron app set up is actually a Togo app that created a web server and using the `toga.WebView()` to bring in the interface. Notice that the `DJANGO_SETTINGS_MODULE` is set to `webapp.webapp.settings`.

## Make sure the Django App is running

If you look into the `positron/src/webapp` folder, there is a typical Django project there (with the `manage.py`). To test if Django is called in properly, we need to first set up a `SECRET_KEY`.

When we start a Django project, this key is generated for us automatically, since we are using a template, we now have to generate this key ourself. For the purpose of this workshop, we will just use the build in methods in Django to do it (same method as when we start a new Django project). As a command it is:

```
$ python -c 'from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())'
```

Copy the secret generated into the `SECRET_KEY` in `positron/src/webapp/webapp/settings.py`

Now, if you run:

```
$ briefcase dev
```

You should see the Django "rocket" running in your app. It means that the Django Positron is working.

## Bring the Django app over to BeeWare

From here, there are two options. We use the [Django tutorial app](https://docs.djangoproject.com/en/4.2/intro/tutorial01/) as an example but it follows similar principles if you are using your own Django app.

---

### 1) develop your app from here

Go to `positron/src/webapp` and use the command:

```
$ cd positron/src/webapp
$ python -m manage.py startapp polls
```

To add a new app `polls` and develop the Django tutorial app from there. You have to make sure that the module paths matches with the new project structure (see option 2 below)

### 2) copy your app over

If you are using the Django tutorial app, replace everything in the `webapp` folder (the one containing `manage.py`) with everything in `mysite` (the one containing `manage.py`). You need to make changes to some module paths to make it matches with the new project structure, check the following:

- in `positron/src/positron/app.py`: `os.environ["DJANGO_SETTINGS_MODULE"] = "webapp.mysite.settings"` and `self.web_view.url = f"http://{host}:{port}/polls/"` (to make the default url linked to the `polls` app)

- in `positron/src/webapp/mysite/settings.py`: `ROOT_URLCONF = 'webapp.mysite.urls'`, `WSGI_APPLICATION = 'mysite.wsgi.application'` and `"webapp.polls.apps.PollsConfig",`

- in `positron/src/webapp/mysite/urls.py`: `path("polls/", include("webapp.polls.urls")),`

- in `positron/src/webapp/polls/apps.py`: `name = 'webapp.polls'`

---

Now, run the command:

```
$ briefcase dev
```

You should see the positron app bring you to the first page of your app.

## Packaging and making it mobile

When our app looks good and ready to deploy, we will follow the rest of the steps of the BeeWare tutorial to [packaging](https://docs.beeware.org/en/latest/tutorial/tutorial-3.html) and [make it mobile](https://docs.beeware.org/en/latest/tutorial/tutorial-5/index.html). If you like the tutorial so far, make sure to check the rest of the BeeWare tutorial to learn more about BeeWare.

## Conclusion and alternatives

Congratulation, now you have successfully develop a positron app with Django. This app is using a local database server as a backend. If you would like to share the data of multiple apps in multiple devices, would that be possible?

The answer is yes, there are various approach you can do so. The first is of cause, migrate your Django backend to the cloud. However, the app will not be usable when it is offline and have no connection to the cloud.

The other approach would be making API calls and requests to fetch the data from the cloud database to update the local database when it's online (and vice versa). With this approach if the device is offline you can still keep a local "cache" in the machine.

Is the second approach the right way to do? No, both approaches will have it's own challenge and limitations. The choice should depend on your use-case and the design of your application. However, this is beyond the scope of this short workshop.

---

Did you enjoy the workshop? I will probably adding more content and extend this workshop in the future, feed backs are welcome.
