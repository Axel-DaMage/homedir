# ADEV.md

## Reglas Normativas Unicas
1. Modo por defecto: cada iteracion debe salir en una rama dedicada y un unico PR atomico con objetivo claro.
2. Todo cambio debe llegar por PR; no se permite push directo a `main`.
3. Los commits deben ser atomicos y con `Conventional Commits`.
4. No mezclar en el mismo PR: refactor + feature + cambios visuales + infraestructura.
5. Mantener look and feel, estructura CSS y HTML del producto salvo instruccion explicita en contrario.
6. Reutilizar y extender la capa de persistencia de Homedir; evitar servicios externos salvo requerimiento explicito.
7. Minimizar impacto sobre APIs/servicios existentes y mantener compatibilidad hacia atras cuando aplique.
8. Si otra persona/agente modifica archivos locales, no tocar los mismos archivos en paralelo.
9. Resolver conflictos preservando mejoras validas de ambos lados; no perder mejoras por sobreescritura.
10. Ejecutar validaciones antes de commit y no incluir archivos no relacionados en el PR.
11. No avanzar a una nueva iteracion sin validar la anterior en produccion.
12. CI obligatoria en verde antes de merge.
13. El versionado y tagging se ejecuta en cadencia manual por iniciativa; no asumir tag por cada PR o cambio menor.
14. La publicacion de releases se realiza en cadencia manual cuando una iniciativa o corte de version lo requiera; no por defecto tras cada PR.
15. Al versionar, actualizar referencias de version en todo el repo (iniciando por `pom.xml` cuando aplique).
16. Documentacion del repo debe mantenerse en estructura bilingue oficial:
    - Canonico: `docs/en/**`
    - Espejo: `docs/es/**` (traduccion o stub)
    - Gateway: `docs/README.md`
17. Todo resultado debe seguirse hasta verificacion de despliegue en produccion.
18. Para nuevas funciones, endpoints o APIs, aplicar rollout incremental obligatorio en 3 iteraciones:
    - Iteracion 1: introducir recurso nuevo oculto/sin consumo productivo.
    - Iteracion 2: integrar consumo progresivo del recurso nuevo.
    - Iteracion 3: retirar versiones legadas/deprecadas una vez validado en produccion.
19. La calidad objetivo de entrega debe mantenerse sobre 95% de exito semanal (ultimos 7 dias) tanto en checks de PR como en pases a produccion; si baja, priorizar estabilizacion antes de nuevas features.
20. Si el proyecto es multilenguaje, todo texto visible debe implementarse via archivos/bundles de idioma; evitar texto hardcoded de forma global en templates, JS, backend y mensajes UI.
21. Override explicito permitido: si se solicita trabajar/ejecutar en modo "entrega por lotes" (o equivalente), se permiten multiples iteraciones atomicas dentro de un solo PR, con validaciones intermedias por etapa y un punto de restauracion para rollback completo del lote.
22. Toda falla de PR, bloqueo de integracion o incidente generado por un cambio debe cerrarse incorporando el aprendizaje resultante en `ADEV.md` como regla o principio consolidado, evitando redundancia y duplicidad.
23. Todo cambio que toque templates, copy visible, i18n, rutas publicas o vistas admin debe actualizar o crear pruebas del comportamiento afectado en la misma iteracion; no se permite dejar assertions heredadas contra UI anterior.
24. Antes de abrir un PR, revisar cambios nuevos contra patrones comunes de CodeQL del repo (logs con input/path, redirects, auth/session, persistencia y URLs derivadas de input); sanitizar o encapsular esos casos en la misma iteracion, no despues del fallo en CI.
25. Para automatizaciones de marketing y publicacion social, el rollout es obligatorio en 3 etapas: borradores internos/sin publicacion -> aprobacion o programacion controlada -> autopublicacion por canal solo tras validacion en produccion.
26. El marketing automatizado solo puede usar datos reales y verificables del producto (releases, metrics, insights, eventos, comunidad, challenges); no inventar cifras, hitos ni claims.
27. Toda integracion de publicacion a redes debe operar con secretos gestionados fuera del repo, deduplicacion de mensajes, rate limit por canal y kill switch global antes de habilitar cualquier scheduler productivo.
28. Toda iteracion, lote u objetivo debe cerrar con handoff actualizado en el workspace compartido y con un PR abierto o actualizado; no se considera trabajo completo si el estado queda solo en el chat o solo en el arbol local.
29. El workspace compartido debe mantenerse consistente en cada checkpoint relevante:
    - actualizar `LATEST.txt`
    - agregar o actualizar `HANDOFF.md` y `state.json`
    - anexar entrada en `SESSION-LOG.md`
    - registrar decisiones durables en `DECISIONS.md` cuando apliquen
30. Antes de pedir aprobacion, merge o paso a produccion, completar las tareas de calidad necesarias para sostener la metrica de exito de PRs:
    - validaciones locales enfocadas al alcance
    - pruebas afectadas por UI, i18n, backend y flujos operativos
    - preflight de CodeQL y sanitizacion de riesgos conocidos
    - documentacion/handoff/plan de verificacion en produccion actualizados
31. El paso a produccion de un PR requiere esta secuencia minima: validacion local completa -> handoff compartido al dia -> PR actualizado -> aprobacion explicita -> merge/auto-merge -> verificacion en produccion.
32. Todo PR debe quedar configurado con `auto-merge`; no cerrar una iteracion dejando un PR manual sin esta configuracion, salvo bloqueo explicito documentado en el handoff y en el propio PR.
33. Todo cambio debe finalizar en un PR al terminar una iteracion u objetivo; no dejar cambios completados solo en rama local, solo en handoff o acumulados sin PR de salida.
34. Todo PR aprobado y mergeado a `main` debe cerrar con limpieza operativa:
    - verificar que el merge a `main` se realizo correctamente
    - eliminar la rama origen del PR en remoto y local cuando ya no sea necesaria
    - actualizar el handoff compartido con el estado post-merge y la limpieza realizada
35. La calidad de PRs, gates de release y validaciones de paso a produccion pertenecen al SDLC y a la operacion de entrega, no al producto user-facing: resolverlos con validaciones locales, ramas feature, CI/CD, runbooks y handoff compartido; no crear dashboards, checks o consolas dentro de Homedir salvo requerimiento explicito y separado del roadmap de producto.
36. Si existe un PRD o roadmap de producto vigente, las iteraciones deben priorizar valor visible para usuarios finales y evitar desviar alcance hacia tooling interno, evidencia de release o capas operativas que no sean parte del producto acordado.

## Flujo Operativo
1. Sincronizar con `origin/main`.
2. Crear rama corta con scope explicito (`feat/*`, `fix/*`, `hotfix/*`, `docs/*`, `chore/*`).
3. Elegir modo de entrega:
   - Default: 1 iteracion = 1 PR.
   - "Entrega por lotes": varias iteraciones atomicas en 1 PR, solo si fue solicitado explicitamente.
4. Implementar solo el alcance acordado para la iteracion (o para la etapa actual del lote).
5. Para nuevas funciones/endpoints/APIs, ejecutar secuencia incremental: oculto/sin uso -> integrado/consumido -> limpieza legado/deprecado.
6. En modo "entrega por lotes", crear punto de restauracion al inicio del lote (tag/commit de referencia) y mantener checkpoints por etapa.
7. Ejecutar validacion local rapida (build y pruebas criticas del cambio) en cada iteracion/etapa.
8. Commit atomico.
9. Push de rama.
10. Actualizar el workspace compartido con handoff consistente del estado actual antes de pedir revision o cambiar de asistente/sesion.
11. Crear o actualizar PR con:
   - Summary
   - Why
   - Scope (in/out)
   - Validation
   - Production verification plan
   - Rollback plan
12. Activar `auto-merge` en todos los PRs cuando los checks requeridos esten listos y exista aprobacion explicita cuando la iniciativa lo requiera.
13. Monitorear `PR Validation` y, when aplique a la iniciativa, el workflow manual de release/produccion correspondiente.
14. Antes de solicitar merge o paso a produccion, ejecutar y dejar registradas las validaciones locales enfocadas al alcance del cambio para reducir fallas en checks del PR y sostener objetivo de exito >95%.
15. Tras aprobacion y merge, verificar en produccion.
16. Tras confirmar que el merge a `main` fue correcto, eliminar la rama origen del PR en remoto y local.
17. Actualizar el handoff compartido.
18. Si falla produccion: rollback y PR correctivo.
