# STM32_Printf
Using printf/scanf for serial debugging on STM32 MCUs

## Installation

### Step 1: Add the code to your project

Add the following code to your UART initialization file (typically `main.c` or `uart.c`):

```c
#ifdef __GNUC__
#define PUTCHAR_PROTOTYPE int __io_putchar(int ch)
#define GETCHAR_PROTOTYPE int __io_getchar(void)
#else
#define PUTCHAR_PROTOTYPE int fputc(int ch, FILE *f)
#define GETCHAR_PROTOTYPE int fgetc(FILE *f)
#endif

PUTCHAR_PROTOTYPE
{
  HAL_UART_Transmit(&huartX, (uint8_t *)&ch, 1, HAL_MAX_DELAY);
  return ch;
}

GETCHAR_PROTOTYPE
{
  uint8_t ch = 0;
  /* Clear the Overrun flag just before receiving the first character */
  __HAL_UART_CLEAR_OREFLAG(&huartX);
  /* Wait for reception of a character on the USART RX line and echo this
   * character on console */
  HAL_UART_Receive(&huartX, (uint8_t *)&ch, 1, HAL_MAX_DELAY);
  HAL_UART_Transmit(&huartX, (uint8_t *)&ch, 1, HAL_MAX_DELAY);
  return ch;
}
```

### Step 2: Configure the UART handler

Replace `huartX` with your actual UART handler:
- `huart1` for USART1
- `huart2` for USART2

### Step 3: Use printf in your code

Now you can use `printf()` normally in your code:
```c
printf("System started successfully!\r\n");
printf("Sensor value: %d\r\n", sensor_value);
```
