# CAPEv2 (capemon) hook(s) generator

This script automatically generates new hooks (hookdefs) for capemon, the CAPEv2 monitor. The hooks are generated according to the info specified in [this repo](https://github.com/reverseame/capemon?tab=readme-ov-file#how-to-add-hooks-to-capemon) (now included in the official [CAPE](https://github.com/kevoreilly/capemon?tab=readme-ov-file#how-to-add-hooks-to-capemon) repo). Please bear in mind the generated files are just skeletons to help you define new hooks, you must adapt them according to your needs.

## Usage

The script requires close to zero configuration. If you want it to Google for the APIs not present in the downloaded `winapi_categories.json` file, just specify your Google API and Google CSE keys in the `config.ini` file. 

Run `python3 generate_hooks.py -h` to print more information.

## How does it work?
The script performs the following steps:

1. Check if API kes have been specified in `config.ini` to determine whether Google search is enabled.
2. Checks whether [`winapi_categories.json`](https://github.com/reverseame/winapi-categories) file is present in the current directory. If it isn't, the script downloads it. 
3. Checks whether the original [`hooks.c`](https://github.com/kevoreilly/capemon) file is present in the current directory. If it isn't, the script downloads it. 
4. Parses `hooks.c` to get the APIs already hooked by CAPEv2.
5. Creates the skeleton files extended_hooks.c, extended_hooks.h and extended_hook_misc.c.
6. If `--dll` is specified, parse each path, extract its EAT and jump to step 8.
7. If `--api` is specified, parse each API call.
8. For every API call, check if it is already present in `hooks.c`. If it isn't try to generate its hook skeleton.
9. Look if the API call is present in `winapi_categories.json`. If it is, read its data, write the files and go back to 8 with the next API call.
10. If the Google search is enabled, Google for it in `site:learn.microsoft.com`. Scrape the first result (we assume it is the correct one), parse the data and go back to 8 with the next API call. If there are no Google results, go back to 8 with the next API call.

## Example
Let's say you already configured your keys in the `config.ini` file (so the script uses Google to scrape learn.microsoft.com for the entries not present in the JSON file) and you want to generate capemon hooks skeleton for the following APIs: CloseHandle, Process32First, Process32Next, ExitProcess, GetProcAddress, GetModuleHandleA, GetModuleHandleW, Sleep, CreateMutexA, CreateMutexW, GetVolumeInformationA and GetVolumeInformationW. You would run the script like so:

```
python3 generate_hooks.py --api CloseHandle,Process32First,Process32Next,ExitProcess,GetProcAddress,GetModuleHandleA,GetModuleHandleW,Sleep,CreateMutexA,CreateMutexW,GetVolumeInformationA,GetVolumeInformationW
```

The script produces three files. Namely, extended_hooks.c, extended_hooks.h and extended_hook_misc.c with the following contents

### extended_hooks.c example
```c
/*
	File autogenerated by capemon hook generator (https://github.com/RazviOverflow/cape-hook-generator)
	The contents of this file can be appended to any array present in your local hooks.c.
	For example, hook_t full_hooks[].
*/

HOOK(kernel32, CloseHandle),
HOOK(kernel32, Process32First),
HOOK(kernel32, Process32Next),
HOOK(kernel32, ExitProcess),
HOOK(kernel32, GetProcAddress),
HOOK(kernel32, GetModuleHandleA),
HOOK(kernel32, GetModuleHandleW),
HOOK(kernel32, Sleep),
HOOK(kernel32, CreateMutexA),
HOOK(kernel32, CreateMutexW),
HOOK(kernel32, GetVolumeInformationA),
HOOK(kernel32, GetVolumeInformationW),
```

### extended_hooks.h example
```c
/*
	File autogenerated by capemon hook generator (https://github.com/RazviOverflow/cape-hook-generator)
	The contents of this file can be appended to your local hooks.h
	WINAPI calling convention is assumed, but it might be incorrect!
*/

HOOKDEF(BOOL, WINAPI, CloseHandle,
	_In_ HANDLE hObject
);

HOOKDEF(BOOL, WINAPI, Process32First,
	_In_ HANDLE hSnapshot,
	_Inout_ LPPROCESSENTRY32 lppe
);

HOOKDEF(BOOL, WINAPI, Process32Next,
	_In_ HANDLE hSnapshot,
	_Out_ LPPROCESSENTRY32 lppe
);

HOOKDEF(VOID, WINAPI, ExitProcess,
	_In_ UINT uExitCode
);

HOOKDEF(FARPROC, WINAPI, GetProcAddress,
	_In_ HMODULE hModule,
	_In_ LPCSTR lpProcName
);

HOOKDEF(HMODULE, WINAPI, GetModuleHandleA,
	_In_opt_ LPCSTR lpModuleName
);

HOOKDEF(HMODULE, WINAPI, GetModuleHandleW,
	_In_opt_ LPCWSTR lpModuleName
);

HOOKDEF(VOID, WINAPI, Sleep,
	_In_ DWORD dwMilliseconds
);

HOOKDEF(HANDLE, WINAPI, CreateMutexA,
	_In_opt_ LPSECURITY_ATTRIBUTES lpMutexAttributes,
  _In_           BOOL                  bInitialOwner,
  _In_opt_ LPCSTR                lpName
);

HOOKDEF(HANDLE, WINAPI, CreateMutexW,
	_In_opt_ LPSECURITY_ATTRIBUTES lpMutexAttributes,
  _In_           BOOL                  bInitialOwner,
  _In_opt_ LPCWSTR               lpName
);

HOOKDEF(BOOL, WINAPI, GetVolumeInformationA,
	_In_opt_  LPCSTR  lpRootPathName,
  _Out_opt_ LPSTR   lpVolumeNameBuffer,
  _In_            DWORD   nVolumeNameSize,
  _Out_opt_ LPDWORD lpVolumeSerialNumber,
  _Out_opt_ LPDWORD lpMaximumComponentLength,
  _Out_opt_ LPDWORD lpFileSystemFlags,
  _Out_opt_ LPSTR   lpFileSystemNameBuffer,
  _In_            DWORD   nFileSystemNameSize
);

HOOKDEF(BOOL, WINAPI, GetVolumeInformationW,
	_In_opt_  LPCWSTR lpRootPathName,
  _Out_opt_ LPWSTR  lpVolumeNameBuffer,
  _In_            DWORD   nVolumeNameSize,
  _Out_opt_ LPDWORD lpVolumeSerialNumber,
  _Out_opt_ LPDWORD lpMaximumComponentLength,
  _Out_opt_ LPDWORD lpFileSystemFlags,
  _Out_opt_ LPWSTR  lpFileSystemNameBuffer,
  _In_            DWORD   nFileSystemNameSize
);
```

### extended_hook_misc.c example
```c
/*
	File autogenerated by capemon hook generator (https://github.com/RazviOverflow/cape-hook-generator)
	The contents of this file can be appended to your local hook_{{category}}.c they belong.
	WINAPI calling convention is assumed, but it might be incorrect!
*/

HOOKDEF(BOOL, WINAPI, CloseHandle,
	_In_ HANDLE hObject
){
	DebuggerOutput("[***** DEBUG MESSAGE - EXTENDED HOOKS *****] Hooked CloseHandle\n");
	BOOL ret = Old_CloseHandle(hObject);
	LOQ_bool("misc", ""); // Modify category, LOQ_ function and log message according to your needs
	return ret;
}

HOOKDEF(BOOL, WINAPI, Process32First,
	_In_ HANDLE hSnapshot,
	_Inout_ LPPROCESSENTRY32 lppe
){
	DebuggerOutput("[***** DEBUG MESSAGE - EXTENDED HOOKS *****] Hooked Process32First\n");
	BOOL ret = Old_Process32First(hSnapshot,lppe);
	LOQ_bool("misc", ""); // Modify category, LOQ_ function and log message according to your needs
	return ret;
}

HOOKDEF(BOOL, WINAPI, Process32Next,
	_In_ HANDLE hSnapshot,
	_Out_ LPPROCESSENTRY32 lppe
){
	DebuggerOutput("[***** DEBUG MESSAGE - EXTENDED HOOKS *****] Hooked Process32Next\n");
	BOOL ret = Old_Process32Next(hSnapshot,lppe);
	LOQ_bool("misc", ""); // Modify category, LOQ_ function and log message according to your needs
	return ret;
}

HOOKDEF(VOID, WINAPI, ExitProcess,
	_In_ UINT uExitCode
){
	DebuggerOutput("[***** DEBUG MESSAGE - EXTENDED HOOKS *****] Hooked ExitProcess\n");
	Old_ExitProcess(uExitCode);
	LOQ_bool("misc", ""); // Modify category, LOQ_ function and log message according to your needs
}

HOOKDEF(FARPROC, WINAPI, GetProcAddress,
	_In_ HMODULE hModule,
	_In_ LPCSTR lpProcName
){
	DebuggerOutput("[***** DEBUG MESSAGE - EXTENDED HOOKS *****] Hooked GetProcAddress\n");
	FARPROC ret = Old_GetProcAddress(hModule,lpProcName);
	LOQ_bool("misc", ""); // Modify category, LOQ_ function and log message according to your needs
	return ret;
}

HOOKDEF(HMODULE, WINAPI, GetModuleHandleA,
	_In_opt_ LPCSTR lpModuleName
){
	DebuggerOutput("[***** DEBUG MESSAGE - EXTENDED HOOKS *****] Hooked GetModuleHandleA\n");
	HMODULE ret = Old_GetModuleHandleA(lpModuleName);
	LOQ_bool("misc", ""); // Modify category, LOQ_ function and log message according to your needs
	return ret;
}

HOOKDEF(HMODULE, WINAPI, GetModuleHandleW,
	_In_opt_ LPCWSTR lpModuleName
){
	DebuggerOutput("[***** DEBUG MESSAGE - EXTENDED HOOKS *****] Hooked GetModuleHandleW\n");
	HMODULE ret = Old_GetModuleHandleW(lpModuleName);
	LOQ_bool("misc", ""); // Modify category, LOQ_ function and log message according to your needs
	return ret;
}

HOOKDEF(VOID, WINAPI, Sleep,
	_In_ DWORD dwMilliseconds
){
	DebuggerOutput("[***** DEBUG MESSAGE - EXTENDED HOOKS *****] Hooked Sleep\n");
	Old_Sleep(dwMilliseconds);
	LOQ_bool("misc", ""); // Modify category, LOQ_ function and log message according to your needs
}

HOOKDEF(HANDLE, WINAPI, CreateMutexA,
	_In_opt_ LPSECURITY_ATTRIBUTES lpMutexAttributes,
  _In_           BOOL                  bInitialOwner,
  _In_opt_ LPCSTR                lpName
){
	DebuggerOutput("[***** DEBUG MESSAGE - EXTENDED HOOKS *****] Hooked CreateMutexA\n");
	HANDLE ret = Old_CreateMutexA(lpMutexAttributes,bInitialOwner,lpName);
	LOQ_bool("misc", ""); // Modify category, LOQ_ function and log message according to your needs
	return ret;
}

HOOKDEF(HANDLE, WINAPI, CreateMutexW,
	_In_opt_ LPSECURITY_ATTRIBUTES lpMutexAttributes,
  _In_           BOOL                  bInitialOwner,
  _In_opt_ LPCWSTR               lpName
){
	DebuggerOutput("[***** DEBUG MESSAGE - EXTENDED HOOKS *****] Hooked CreateMutexW\n");
	HANDLE ret = Old_CreateMutexW(lpMutexAttributes,bInitialOwner,lpName);
	LOQ_bool("misc", ""); // Modify category, LOQ_ function and log message according to your needs
	return ret;
}

HOOKDEF(BOOL, WINAPI, GetVolumeInformationA,
	_In_opt_  LPCSTR  lpRootPathName,
  _Out_opt_ LPSTR   lpVolumeNameBuffer,
  _In_            DWORD   nVolumeNameSize,
  _Out_opt_ LPDWORD lpVolumeSerialNumber,
  _Out_opt_ LPDWORD lpMaximumComponentLength,
  _Out_opt_ LPDWORD lpFileSystemFlags,
  _Out_opt_ LPSTR   lpFileSystemNameBuffer,
  _In_            DWORD   nFileSystemNameSize
){
	DebuggerOutput("[***** DEBUG MESSAGE - EXTENDED HOOKS *****] Hooked GetVolumeInformationA\n");
	BOOL ret = Old_GetVolumeInformationA(lpRootPathName,lpVolumeNameBuffer,nVolumeNameSize,lpVolumeSerialNumber,lpMaximumComponentLength,lpFileSystemFlags,lpFileSystemNameBuffer,nFileSystemNameSize);
	LOQ_bool("misc", ""); // Modify category, LOQ_ function and log message according to your needs
	return ret;
}

HOOKDEF(BOOL, WINAPI, GetVolumeInformationW,
	_In_opt_  LPCWSTR lpRootPathName,
  _Out_opt_ LPWSTR  lpVolumeNameBuffer,
  _In_            DWORD   nVolumeNameSize,
  _Out_opt_ LPDWORD lpVolumeSerialNumber,
  _Out_opt_ LPDWORD lpMaximumComponentLength,
  _Out_opt_ LPDWORD lpFileSystemFlags,
  _Out_opt_ LPWSTR  lpFileSystemNameBuffer,
  _In_            DWORD   nFileSystemNameSize
){
	DebuggerOutput("[***** DEBUG MESSAGE - EXTENDED HOOKS *****] Hooked GetVolumeInformationW\n");
	BOOL ret = Old_GetVolumeInformationW(lpRootPathName,lpVolumeNameBuffer,nVolumeNameSize,lpVolumeSerialNumber,lpMaximumComponentLength,lpFileSystemFlags,lpFileSystemNameBuffer,nFileSystemNameSize);
	LOQ_bool("misc", ""); // Modify category, LOQ_ function and log message according to your needs
	return ret;
}
```
## Limitations
The script requires internet connection to be able to download both `winapi_categories.json` and `hooks.c` files, and also to use Google (in case it is configured).

Another limiation is the quota / search rate imposed by Google. As of right now, Google CSE (Custom Search Engine) API limits the automation to 100 searches per day.

**WINAPI** calling convention is assumed. Modify autogenerated files as needed.

## Acknowledgments
Thanks to [Kevin O'Reilly](https://github.com/kevoreilly) and [Andriy Brukhovetskyy (a.k.a doomedraven)](https://github.com/doomedraven).

## Comments
- Changes to `config.ini` are ommited with `git update-index --skip-worktree config.ini`.