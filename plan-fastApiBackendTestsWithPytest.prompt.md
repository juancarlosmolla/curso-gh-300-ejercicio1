## Plan: Tests backend FastAPI con pytest

Añadir una suite de tests backend en una carpeta `tests/`, usando `pytest` y `fastapi.testclient.TestClient`, con estructura AAA (Arrange-Act-Assert) consistente por endpoint. También actualizar `requirements.txt` para declarar `pytest`, ya que `pytest.ini` ya existe y el proyecto aún no lo lista como dependencia.

**Steps**
1. Crear la carpeta `tests/` en la raíz del proyecto y separar los tests por superficie HTTP: raíz, listado de actividades, registro y baja de participantes.
2. Añadir `tests/conftest.py` con una fixture `client` basada en `TestClient(app)` y una fixture/autouse para restaurar el diccionario global `activities` entre tests; esto bloquea el resto de tests porque el backend persiste estado en memoria.
3. Crear `tests/test_root.py` para verificar la redirección de `GET /` a `/static/index.html` usando AAA.
4. Crear `tests/test_activities.py` para cubrir `GET /activities`, validando código de estado, forma de la respuesta y presencia de campos esperados por actividad; esto puede avanzar en paralelo con el paso 3.
5. Crear `tests/test_signup.py` para cubrir `POST /activities/{activity_name}/signup` con al menos estos casos: alta correcta, actividad inexistente y alta duplicada; usar AAA explícito dentro de cada test. Este paso depende del reseteo de estado del paso 2.
6. Crear `tests/test_unregister.py` para cubrir `DELETE /activities/{activity_name}/signup` con al menos estos casos: baja correcta, actividad inexistente y participante no inscrito; también depende del paso 2.
7. Actualizar `requirements.txt` para incluir `pytest` junto con las dependencias backend existentes.
8. Ejecutar `pytest` desde la raíz para validar la nueva suite y corregir solo fallos ligados a los tests añadidos.

**Relevant files**
- `/workspaces/curso-gh-300-ejercicio1/src/app.py` — reutilizar la app FastAPI, el diccionario global `activities` y el comportamiento actual de los endpoints `root`, `get_activities`, `signup_for_activity` y `unregister_from_activity`.
- `/workspaces/curso-gh-300-ejercicio1/requirements.txt` — añadir `pytest` como dependencia del proyecto.
- `/workspaces/curso-gh-300-ejercicio1/pytest.ini` — ya existe; reutilizar su configuración actual (`pythonpath = .`) sin duplicarla en otro sitio.
- `/workspaces/curso-gh-300-ejercicio1/tests/conftest.py` — centralizar fixtures compartidas y aislamiento de estado.
- `/workspaces/curso-gh-300-ejercicio1/tests/test_root.py` — cobertura de la redirección inicial.
- `/workspaces/curso-gh-300-ejercicio1/tests/test_activities.py` — cobertura del listado de actividades.
- `/workspaces/curso-gh-300-ejercicio1/tests/test_signup.py` — cobertura de altas con patrón AAA.
- `/workspaces/curso-gh-300-ejercicio1/tests/test_unregister.py` — cobertura de bajas con patrón AAA.

**Verification**
1. Ejecutar `pytest` en la raíz del workspace y confirmar que la suite pasa completa.
2. Revisar que cada test esté organizado en bloques AAA claros y legibles.
3. Confirmar que el estado de `activities` queda restaurado entre tests para evitar dependencias de orden.
4. Verificar que `requirements.txt` contiene `pytest` y que la instalación de dependencias permite correr la suite en un entorno limpio.

**Decisions**
- Se usará `pytest` como framework de tests, no `unittest`.
- Los tests vivirán en una carpeta separada `tests/` en la raíz, no dentro de `src/`.
- Se probará el comportamiento actual del backend sin ampliar alcance a validación de email o control de aforo, porque esas reglas no existen hoy en `src/app.py`.
- La estructura AAA se aplicará dentro de cada test mediante bloques claros de preparación, acción y aserción.

**Further Considerations**
1. Si se quiere reforzar consistencia de estilo, puede añadirse una convención mínima al nombre de tests (`test_should_...`) durante la implementación, pero no es necesaria para arrancar.
2. Si luego aparece más lógica de negocio en memoria, convendrá extraer el estado a una dependencia inyectable para simplificar el aislamiento de tests.
