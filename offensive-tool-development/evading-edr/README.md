---
description: >-
  I'll try to keep this section's focus on the most-modern methods for evading
  EDR.
---

# Evading EDR

## References

Here are some fantastic articles that I've discovered throughout my research into EDR's.

{% embed url="https://synzack.github.io/Blinding-EDR-On-Windows/" %}

{% embed url="https://www.microsoft.com/en-us/security/blog/2022/06/30/using-process-creation-properties-to-catch-evasion-techniques/" %}

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>Taken from Microsoft's Blog (Link above)</p></figcaption></figure>



## The EDR Process

EDR's work by utilizing kernel callbacks to inject Hooks into newly created processes. Here's how it works:&#x20;

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

To determine what the file is about to execute as a process, the antimalware engine scans the file’s content at the time of process creation through a process creation callback. Antimalware engines also scan freshly edited files once a handle to that file has been closed.



## Kernel Callback Functions

Callbacks, in simple terms, is just a function that isn't directly invoked by the developer. Instead, it is invoked when a specific event occurs. Some events for Kernel Callbacks are:

Process / thread creation, obtaining a handle, setting a registry key, closing process / thread, or any other activities.

### How Kernel Callbacks are created

Kernel callbacks in Windows involves developing kernel-mode drivers. These drivers are able to register various callbacks to receive notifications or intercept events in the kernel. While it's not the on;y way to interact with kernel callbacks, developing a kernel-mode driver is a common approach.



Microsoft provides a variety of functions that allow callbacks to be registered and intercept events in the kernel.

{% embed url="https://learn.microsoft.com/en-us/windows-hardware/drivers/ddi/ntddk/nf-ntddk-pssetcreateprocessnotifyroutineex?redirectedfrom=MSDN" %}
On Process Creation
{% endembed %}

Process creation notification isn’t invoked right when a process creation API is called, but rather when the first thread is inserted into a process.

{% embed url="https://learn.microsoft.com/en-us/windows-hardware/drivers/ddi/ntddk/nf-ntddk-pssetloadimagenotifyroutine" %}
On Image Load (DLL or EXE)
{% endembed %}





## References

Here is a good blog to learn more

{% embed url="https://jsecurity101.medium.com/understanding-telemetry-kernel-callbacks-1a97cfcb8fb3" %}
