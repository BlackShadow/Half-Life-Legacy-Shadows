# Half-Life-Legacy-Shadows
Legacy/WON shadows of Half-Life

Hey everyone, i'm sharing this tutorial for getting old shadows in newer engine builds. (Steampipe and later)

There was a assembly hack to enable shadows in Half-Life but it's no longer works in newer engine and crashes the game. The reason is newer engine build have different signature.

So let's get started.

First let's start with StudioModelRenderer.cpp

First find these lines:

```
 // Global engine <-> studio model rendering code interface
engine_studio_api_t IEngineStudio;
```

And paste these codes after those lines:

```
void (*GL_StudioDrawShadow)(void);

__declspec(naked) void ShadowHack(void)
{
	_asm
	{
		push ebp;
		mov ebp, esp;
		push ecx;
		jmp[GL_StudioDrawShadow];
	}
}
```

Now we need to find these lines: 

(these codes are in bottom so scroll down a bit)

```
====================
StudioRenderFinal_Hardware

====================
*/
void CStudioModelRenderer::StudioRenderFinal_Hardware(void)
{
	int i;
```

Then add this code after int i;
```
int iShadows = CVAR_GET_FLOAT("cl_shadows");
```
After these lines:
```
if (m_fDoInterp)
			{
				// interpolation messes up bounding boxes.
				m_pCurrentEntity->trivial_accept = 0;
			}

			IEngineStudio.GL_SetRenderMode(rendermode);
			IEngineStudio.StudioDrawPoints();
```

Paste these codes:
```
	if (iShadows == 1)

			{

				GL_StudioDrawShadow = (void(*)(void))(((unsigned int)IEngineStudio.GL_StudioDrawShadow) + 35);

				if (IEngineStudio.GetCurrentEntity() != gEngfuncs.GetViewModel())
					ShadowHack();

			}

```

Now we need to create a cvar

Go to your hud.cpp

Find these lines:

```
m_iLogo = 0;
m_iFOV = 0;

```

Paste this code after those lines:

```
CVAR_CREATE("cl_shadows", "1", FCVAR_ARCHIVE);

```

And compile your SDK.

And there you have it. OG Half-Life Shadows running in latest engine build. Shadows are messy from default so you cannot really improve them at all. Some people like it and want in their mods so this should be useful for you. 

If you see any problem post in comments i'll try my best to help you out.
