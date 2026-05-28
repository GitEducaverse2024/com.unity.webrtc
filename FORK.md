# Fork notes - GitEducaverse2024/com.unity.webrtc

> Este repo es un **fork minimo** de `Unity-Technologies/com.unity.webrtc`
> mantenido por **Educaverse SL** para soportar el SDK `com.educa360.live`
> en Unity 6.x.

## ¿Por que este fork existe?

El SDK [Educa360 Live](https://github.com/GitEducaverse2024/PlataformaEduca360/tree/main/UnityLiveSDK)
necesita una version estable de `com.unity.webrtc` que funcione en
**Quest APK con Unity 6.0 LTS+**. Ninguna version oficial publicada hasta
el momento (`3.0.0-pre.7` actual) cumple este requisito a la vez:

| Version oficial | Compila en Unity 6.x | Funciona en Android Quest | Estado |
|---|---|---|---|
| `3.0.0-pre.4` | NO - `AndroidApiLevel22` obsoleto causa CS0619 error | SI - encoder Android estable | rota build Unity 6.x |
| `3.0.0-pre.5+` | SI | NO - encoder no produce bytes ([issue #1085](https://github.com/Unity-Technologies/com.unity.webrtc/issues/1085)) | video negro en APK |
| `3.0.0-pre.4-edu360` (este fork) | **SI** | **SI** | estable en produccion v1.0 |

## ¿Que patches contiene?

### Patch unico: `Editor/BuildProcessor.cs` linea 32

Cambio `AndroidApiLevel22` -> `AndroidApiLevel25`:

```diff
 #if UNITY_2021_1_OR_NEWER
-        public const AndroidSdkVersions RequiredAndroidSdkVersion = AndroidSdkVersions.AndroidApiLevel22;
+        public const AndroidSdkVersions RequiredAndroidSdkVersion = AndroidSdkVersions.AndroidApiLevel25;
 #else
         public const AndroidSdkVersions RequiredAndroidSdkVersion = AndroidSdkVersions.AndroidApiLevel21;
 #endif
```

**Por que**: Unity 6.0+ marca `AndroidSdkVersions.AndroidApiLevel22` como
obsoleto con severidad `CS0619` (error, no warning). El build APK falla
con el mensaje:

```
Library/PackageCache/com.unity.webrtc@.../Editor/BuildProcessor.cs(32,69):
error CS0619: 'AndroidSdkVersions.AndroidApiLevel22' is obsolete:
'Minimum supported Android API level is 25 (Android 7.1 Nougat).
Please use AndroidApiLevel25 or higher.'
```

El cambio a `AndroidApiLevel25` es semantica de "minimo recomendado" — el
package solo lo usa para emitir warnings al dev cuando su Player Settings
tiene un Min API muy bajo; no afecta funcionalidad runtime.

## ¿Por que NO modificamos otras cosas?

Mantener el fork **lo mas pequenyo posible** simplifica:

- Auditoria: cualquiera puede ver el diff completo en `git diff 3.0.0-pre.4..3.0.0-pre.4-edu360`.
- Re-sync con upstream cuando Unity libere un fix oficial.
- Adopcion de terceros que duden de la confianza del fork.

**Cosas que NO tocamos**:

- `package.json` (nombre `com.unity.webrtc`, version `3.0.0-pre.4`) — mantener
  permite que Unity lo trate como sustitucion transparente del oficial.
- Codigo runtime nativo (.cpp, .h, .so, .a).
- Codigo C# fuera de `Editor/BuildProcessor.cs`.
- Documentation, README, license del package original.

## ¿Cuando dejaremos de mantener este fork?

En cuanto Unity publique una version oficial `3.0.0-pre.X` (con X >= 5)
que **funcione en Android Quest** Y **no tenga el bug obsoleto en
`BuildProcessor.cs`**, este fork se vuelve obsoleto. El SDK Educa360 Live
cambiara `package.json -> dependencies -> com.unity.webrtc` al package
oficial y este repo se archivara.

Tracking del issue upstream: [#1085](https://github.com/Unity-Technologies/com.unity.webrtc/issues/1085).

## ¿Como instalar este fork en tu proyecto Unity?

En `Packages/manifest.json` de tu proyecto:

```json
{
  "dependencies": {
    "com.unity.webrtc": "https://github.com/GitEducaverse2024/com.unity.webrtc.git#3.0.0-pre.4-edu360"
  }
}
```

Unity Package Manager soporta git URL nativamente desde Unity 2019.3+.

## Branches y tags

| Ref | Proposito |
|---|---|
| `main` | Espejo del upstream `main`. NO usar para produccion. |
| `3.0.0-pre.4-edu360` | Branch del fork con patches aplicados. **Esta es la version de produccion**. |
| Tag `v3.0.0-pre.4-edu360` | Snapshot estable del branch. Recomendado pinning aqui. |

## Contacto

- **SDK Educa360 Live** (consumidor de este fork): `antonio@educa360.com`
- **Issues del fork**: `https://github.com/GitEducaverse2024/com.unity.webrtc/issues`
- **Issues del package oficial**: `https://github.com/Unity-Technologies/com.unity.webrtc/issues`

## Licencia

Este fork mantiene la licencia original de `Unity-Technologies/com.unity.webrtc`
(ver `LICENSE.md` del repo). Los patches aplicados son contribuciones
mínimas en compliance con dicha licencia.

---

Maintainer: Educaverse SL <antonio@educa360.com>
Creado: 2026-05-28
