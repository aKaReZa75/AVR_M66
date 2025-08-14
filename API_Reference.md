# M66 GSM Module Library Documentation
This library provides a complete interface for controlling the M66 GSM module with ATMEGA328 microcontrollers. It handles all basic operations including module initialization, SMS sending/receiving, and network status checks.

> [!NOTE]  
> The library and all of its APIs have been developed by Hossein Bagheri.  
> This implementation uses macros from `aKaReZa.h` and depends on USART communication.  
> 
> See documentation for:  
> - Core macros: [https://github.com/aKaReZa75/AVR/blob/main/Macros.md](https://github.com/aKaReZa75/AVR/blob/main/Macros.md)  
> - USART library: [https://github.com/aKaReZa75/AVR_USART](https://github.com/aKaReZa75/AVR_USART)  

| Feature                  | Configuration               |
|--------------------------|-----------------------------|
| **Default Baud Rate**    | 115200 bps                   |
| **Command Timeout**      | 300 ms                      |
| **Startup Timeout**      | 20000 ms                    |
| **SMS Format**          | Text Mode (AT+CMGF=1)       |
| **Character Encoding**  | GSM (AT+CSCS="GSM")         |
| **SMS Parameters**      | AT+CSMP=17,167,0,0          |

## AT Command Reference

```c
// Basic Commands
#define __M66_CMD_AT           "AT"          // Module test
#define __M66_CMD_Restart      "AT+QPOWD=0"  // Module restart

// Configuration Commands  
#define __M66_CMD_EchoOff      "ATE0"        // Disable echo
#define __M66_CMD_fullFunc     "AT+CFUN=1"   // Full functionality
#define __M66_CMD_textMode     "AT+CMGF=1"   // SMS text mode

// SMS Commands
#define __M66_CMD_SMSdellAll   "AT+CMGD=1,4" // Delete all SMS
#define __M66_CtrlZ            0x1A          // SMS end marker

// Network Commands
#define __M66_CMD_SignalQua    "AT+CSQ"      // Signal quality
#define __M66_CMD_NetReg       "AT+CREG?"    // Network registration
```

## API Reference

### Initialization Functions

```c
M66_Res_T M66_Init(void);
```
- Performs complete module initialization sequence
- Combines `M66_Startup()` and `M66_Config()`
- Returns: `M66_Res_OK`, `M66_Res_ERR`, or `M66_Res_TimeOut`

**Example:**
```c
if(M66_Init() == M66_Res_OK) 
{
    // Ready for communication
}
```

### Core Operations

```c
M66_Res_T M66_SendSMS(char* _PhoneNumber, char* _Text);
```
- Sends SMS to specified number
- Parameters:
  - `_PhoneNumber`: String in international format (e.g., "+989123456789")
  - `_Text`: Message content (160 char max)
- Returns operation status

**Example:**
```c
M66_SendSMS("+989123456789", "System Online");
```

### Low-Level Control

```c
M66_Res_T M66_SendAtCmd(char* _CMD, char* _Reponse, int16_t _TimeOut);
```
- Direct AT command interface
- Parameters:
  - `_CMD`: Command string
  - `_Reponse`: Expected response
  - `_TimeOut`: Timeout in ms
- Returns command status

**Example:**
```c
// Check signal quality
M66_SendAtCmd(__M66_CMD_SignalQua, "+CSQ:", 1000);
```

## Complete Example

```c
#include "aKaReZa.h"
#include "M66.h"

extern volatile bool usart_RxFlag;   /**< True: Data has been received completely */   
extern char usart_RxBuffer[__usart_RxBufferSize]; /* Buffer to store received data, with defined size */


int main(void)
{
    // Initialize module
    if(M66_Init() != M66_Res_OK) 
    {
        while(1); // Handle failure
    }
    
    // Send boot notification
    M66_SendSMS("+989123456789", "System booted!");
    
    while(1) 
    {
        if(!usart_RxFlag)
        {
          if(strstr(usart_RxBuffer,"+CMTI") != NULL)
          {
            M66_CheckSMS();
          };
        };
    };
};
```

## Response Codes

| Code            | Description                  |
|-----------------|------------------------------|
| `M66_Res_OK`    | Operation successful         |
| `M66_Res_ERR`   | Command failed               |
| `M66_Res_TimeOut` | No response received        |

## Important Notes

1. Always use international format for phone numbers
2. Startup may take up to 20 seconds
3. Module must have valid SIM card and network connection
4. SMS operations require proper configuration first

# 🌟 Support Me
If you found this repository useful:
- Subscribe to my [YouTube Channel](https://www.youtube.com/@aKaReZa75).
- Share this repository with others.
- Give this repository and my other repositories a star.
- Follow my [GitHub account](https://github.com/aKaReZa75).

# ✉️ Contact Me
Feel free to reach out to me through any of the following platforms:
- 📧 [Email: aKaReZa75@gmail.com](mailto:aKaReZa75@gmail.com)
- 🎥 [YouTube: @aKaReZa75](https://www.youtube.com/@aKaReZa75)
- 💼 [LinkedIn: @akareza75](https://www.linkedin.com/in/akareza75)
