# Digitizer Docs

# How to generate the documentation

This documentation is being developed in MkDocs. The provider of the git repo hosting the documents is __github__, which can also host seamlessly a web static page with the documentation. The page can be found [here](https://fabernet.github.io/DIGITIZER-docs/).

In case it is desirable to render the documentation locally, do the following:

- install MkDocs python package (e.g., run: `$ pip install mkdocs`)
- cd into the documentation folder, i.e. the repo root folder
- from the shell (e.g. WSL) launch the MkDocs server: `$ mkdocs serve`
- Doc pages will be locally rendered in a browser at URL: http://127.0.0.1:8000/

To publish any changes to the documentation on the "github.io" page, use the following command:
```
mkdocs gh-deploy
```

Documentation on how to use `MkDocs` can be found [here](https://www.mkdocs.org/).
