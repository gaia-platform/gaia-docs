# gaia-docs

We are using the Microsoft Writing Style Guide as our basic guide to creating content. As we find areas where we need to or want to deviate from the guide we will create our own addendum document. 

Two primary branches
Main - Docs are built out of here using [DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html)
Master - Primary repository for the docs. Can contain docs that aren't released yet. Update PRs here.

index.md in the main folder is the entry doc to the site. Each folder has an index.md file that is the entry point to that area.
toc.yml contains the table of contents for that folder. For information about the structure of the ToC files, see [Table-Of-Content (TOC) Files](https://dotnet.github.io/docfx/tutorial/intro_toc.html) in the DocFx documentation.

Folders:
* api - API reference documentation
* articles - the main topics in the documentation
    * images - Proposed: subfolders containing the images for a specific topic in articles
    * templates - template topics
        *  images - Proposed: subfolders containing the images for a specific topic in templates
    * tutorials - tutorial topics
        *  images - Proposed: subfolders containing the images for a specific topic in templates
* images - DocFx wide images
* src - contains code to customize the DocFx output
* obj - DocFX internal
