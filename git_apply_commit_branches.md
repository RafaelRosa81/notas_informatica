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
