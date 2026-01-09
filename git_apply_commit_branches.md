Cómo funciona (modelo mental simple)

Tu repo local tiene un solo “working directory” (la carpeta con archivos).
Cuando hacés git checkout <rama>, Git reemplaza el contenido de esa carpeta para que refleje esa rama.
git apply modifica los archivos que están en tu working directory en ese momento, o sea: la rama actualmente checkouteada.

En tu caso:

git checkout -b pr-7-excel-area-config
✅ te moviste a esa rama (quedó marcada con *)

git apply ...
✅ aplicaste el patch sobre esa rama (no sobre main)

git commit ...
✅ el commit queda solo en esa rama (pr-7-excel-area-config)

git push origin pr-7-excel-area-config
✅ subiste esa rama a GitHub (para PR)

¿Cómo verifico si main tiene esos cambios?

Hacé esto:

1) Cambiar a main
git checkout main

2) Ver si main está limpio
git status

Debería decir “working tree clean”.

3) Ver si main tiene el commit
git log --oneline -5

Si no aparece b3bfe59, entonces main no lo tiene (y es lo normal).

¿Cuándo pasa a main?
Solo cuando:
Creás el Pull Request en GitHub desde pr-7-excel-area-config hacia main
y mergeás ese PR

o si localmente hacés un merge manual:
git checkout main
git merge pr-7-excel-area-config

Nota importante de tu commit
Vos también commiteaste el archivo excel_area_config.patch (quedó “untracked” y luego git add . lo incluyó).

Si no querés versionar patches en el repo, lo ideal es:

borrarlo del repo en esta rama y agregarlo al .gitignore, o

para el futuro: usar git add -u en lugar de git add .

Para sacarlo ahora (opcional):

git rm excel_area_config.patch
git commit -m "Remove patch file"
git push

Resumen

✅ Patch aplicado y commiteado en pr-7-excel-area-config
❌ main local NO cambia hasta que hagas merge (PR o merge local)



✅Git detecta automáticamente cuando editás un archivo local y te lo indica con:

git status


Verás algo como:

modified: archivo.py


👉 Esto NO depende de git add.

¿Qué hace realmente git add?

NO hace que Git “empiece a mirar” el archivo

SÍ marca el archivo para que entre en el próximo commit (lo “stagea”)

Conceptos clave (en una tabla mental)
Estado del archivo	Qué significa
tracked	Git ya lo conoce (está en algún commit previo)
modified	Cambiaste el archivo localmente
staged (git add)	El cambio está listo para commitear
untracked	Git no lo conoce todavía
Flujo típico
# editás un archivo
git status          # Git ve el cambio

git add archivo.py  # preparás el cambio
git commit -m "msg" # lo guardás en el historial

Regla de oro

Git siempre ve los cambios. git add solo decide cuáles van al commit.
