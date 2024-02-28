# NtQuerySystemInformation (winternl.h)

NtQuerySystemInformation is a syscall and exported from ntdll.dll. Therefore, we need to use GetModuleHandle and GetProcAddress to retrieve NtQuerySystemInformations's address from ntdll.dll.

_**Here is the specification from Microsoft's documentation:**_

```c
__kernel_entry NTSTATUS NtQuerySystemInformation(
  [in]            SYSTEM_INFORMATION_CLASS SystemInformationClass,
  [in, out]       PVOID                    SystemInformation,
  [in]            ULONG                    SystemInformationLength,
  [out, optional] PULONG                   ReturnLength
);
```

_**Retrieve the address from ntdll.dll:**_

```c
// Function pointer
typedef NTSTATUS (NTAPI* fnNtQuerySystemInformation)(
	SYSTEM_INFORMATION_CLASS SystemInformationClass,
	PVOID                    SystemInformation,
	ULONG                    SystemInformationLength,
	PULONG                   ReturnLength
);

fnNtQuerySystemInformation pNtQuerySystemInformation = NULL;

// Getting NtQuerySystemInformation's address
pNtQuerySystemInformation = (fnNtQuerySystemInformation)GetProcAddress(GetModuleHandle(L"NTDLL.DLL"), "NtQuerySystemInformation");
if (pNtQuerySystemInformation == NULL) {
	printf("[!] GetProcAddress Failed With Error : %d\n", GetLastError());
	return FALSE;
}

```



## Full Code Example

In the below example we call NtQuerySystemInformation twice. The first is to determine the length of SYSTEM\_PROCESS\_INFORMATION. The second we write process information to the struct SystemProcInfo using the length we got from the first iteration.

<mark style="color:red;">**NOTE:**</mark> SYSTEM\_PROCESS\_INFORMATION is a struct for 1 process. Each process has access to the next process through "NextEntryOffset"

{% code fullWidth="true" %}
```c
BOOL QuerySystemInformation() {

    fnNtQuerySystemInformation		                        pNtQuerySystemInformation   = NULL;
    ULONG							uReturnLen1                 = 0;
    ULONG                                                       uReturnLen2                 = 0;
    PSYSTEM_PROCESS_INFORMATION		                        SystemProcInfo              = NULL;

    PVOID							pValueToFree                = NULL;
    NTSTATUS						        STATUS                      ;

    
    // Fetching NtQuerySystemInformation's address from ntdll.dll
    pNtQuerySystemInformation = (fnNtQuerySystemInformation)GetProcAddress(GetModuleHandle(L"NTDLL.DLL"), "NtQuerySystemInformation");
    if (pNtQuerySystemInformation == NULL) {
        wprintf(L"[!] GetProcAddress Failed With Error : %d\n", GetLastError());
    }

    // First NtQuerySystemInformation call - retrieve the size of the return buffer (uReturnLen1)
    STATUS = pNtQuerySystemInformation(SystemProcessInformation, NULL, 0, &uReturnLen1);
    if (NT_SUCCESS(STATUS) != TRUE && STATUS != 0xC0000004) {// STATUS_INFO_LENGTH_MISMATCH
        wprintf(L"[!] NtQuerySystemInformation [1] Failed With Error : 0x%0.8X \n", STATUS);
    }

    // Allocating buffer of size "uReturnLen1"
    SystemProcInfo = (PSYSTEM_PROCESS_INFORMATION)HeapAlloc(GetProcessHeap(), HEAP_ZERO_MEMORY, (SIZE_T)uReturnLen1);
    if (SystemProcInfo == NULL) {
        wprintf(L"[!] HeapAlloc Failed With Error : %d\n", GetLastError());
    }

    // Setting a fixed variable to be used later to free, because "SystemProcInfo" will be modefied
    pValueToFree = SystemProcInfo;

    // Second NtQuerySystemInformation call - returning the SYSTEM_PROCESS_INFORMATION array (SystemProcInfo)
    STATUS = pNtQuerySystemInformation(SystemProcessInformation, SystemProcInfo, uReturnLen1, &uReturnLen2);
    if (NT_SUCCESS(STATUS) != TRUE) {
        wprintf(L"[!] NtQuerySystemInformation [2] Failed With Error : 0x%0.8X \n", STATUS);
    } else {
    // Enumerate each process.
        do {
            wprintf(L"%d : %ls\n", HandleToULong(SystemProcInfo->UniqueProcessId), SystemProcInfo->ImageName.Buffer);
            SystemProcInfo = (PSYSTEM_PROCESS_INFORMATION)((PBYTE)SystemProcInfo + SystemProcInfo->NextEntryOffset);

        } while (SystemProcInfo->NextEntryOffset != 0);
    }


}
```
{% endcode %}





<figure><img src="../../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>
