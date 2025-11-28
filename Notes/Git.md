
## Create a new repository on the command line



echo "# laboratorio" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:wt3c/laboratorio.git
git push -u origin main

## Push an existing repository from the command line

git remote add origin git@github.com:wt3c/laboratorio.git
git branch -M main
git push -u origin main