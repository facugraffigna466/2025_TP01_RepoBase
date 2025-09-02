# Proyecto Base para Práctica de Git

Este proyecto es parte del Trabajo Práctico 01 – Git Básico.

Paso 1 –

 Hice fork del repositorio base de la cátedra y cloné mi propia copia con el comando git clone. Esto me permite trabajar todo el TP desde mi cuenta personal de GitHub, así después el profe puede ver bien todas mis ramas, commits y el tag final.

Paso 2 –

Creé una rama llamada feature/nueva-funcionalidad para trabajar la nueva funcionalidad sin afectar directamente el main.

Esto lo hice con el comando: 
git checkout -b feature/nueva-funcionalidad

Trabajar en ramas me permite mantener separado lo nuevo de lo que ya funciona, y después integrar cuando esté todo bien.

Paso 3 -

 Para desarrollar la nueva funcionalidad, fui trabajando en la rama feature/nueva-funcionalidad y dividí el trabajo en 3 commits:
	1.	Crear el archivo vacío contacto.html
	2.	Agregar un encabezado 
	3.	Agregar un campo de tipo nombre

Hice esto para tener un historial ordenado. En caso de fallas trabajar asi es lo optimo para darse cuenta facil donde esta el error.

Paso 4  - 

Me pasé a la rama main para simular un error como si estuviera en producción.
Creé el archivo index.html con un texto simulado de error escrito a propósito para después corregirlo desde una rama hotfix.

Paso 5 - 

Creé la rama hotfix/bug-index para corregir el error que simulé en main.Es una buena práctica trabajar los errores en ramas diferentes para mantener la trazabilidad y evitar trabajar en ramas main. 

Paso 6 - 

Me pasé a la rama main y traje el fix usando git merge hotfix/bug-index.Usé merge en lugar de cherry-pick porque son ramas con historia común y es más seguro integrar así.
Este merge garantiza que todo el historial de cambios quede registrado y entendible para cualquiera que lo lea después.

Paso 7 - 

Me pasé a la rama main y traje el fix usando git merge hotfix/bug-index.

Paso 8 - 

Después de hacer el arreglo en el index.html dentro de la rama main lleve ese arreglo a la rama donde estoy desarrollando la nueva funcionalidad , para tenerlo también ahí y no trabajar sobre algo roto. Use :

git log --oneline
git cherry-pick 5d7e57e

pero me aparecio un error que era que no estaba el index en mi rama entonces git te da dos opciones o agregarlo o dejar como esta todo y yo lo agregue 

git cherry-pick --continue

Paso 9 - 

Pushie para subir todo lo que hice en mi rama.  Esto incluye los commits de la nueva funcionalidad y el cherry-pick que trajimos desde la rama del fix.

Paso 10 - 

Una vez que terminé la funcionalidad en mi rama feature/nueva-funcionalidad, hice la Pull Request correctamente dentro de mi fork ya que me equivoque antes y la hice en la de ingucc y la aprobé yo mismo con el botón verde. Esto une mi trabajo con main, como si lo integrara al proyecto final.

Paso 11 - 

Después de hacer el Pull Request desde fix/index a main y confirmar el merge, nos quedamos con la rama main local desactualizada respecto a GitHub. Por eso hicimos hacemos rebase, pero cuando git empeiza a tarer los commits se encuentra con conflitcos en readme e index para ello use un git add y lo solucione manualmente 

Paso 12 - 

 Cree un tag v1.0.0 para marcar el punto en el que el sistema ya esta perfecto.
Esto nos permite versionar el proyecto y tener una referencia clara para futuras mejoras o despliegues en producción.
