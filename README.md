# Hesap-makinesi

Arduino uno 4x4 tuş takımı I2C 16x2 ekran

kod:

```cpp
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <Keypad.h>

// Set up the I2C LCD (address 0x27, 16 columns and 2 rows)
LiquidCrystal_I2C lcd(0x27, 16, 2);  // Set the LCD address to 0x27 and 16x2 size

// Define the keypad's rows and columns
const byte ROW_NUM    = 4; // Four rows
const byte COL_NUM    = 4; // Four columns

char keys[ROW_NUM][COL_NUM] = {
  {'1', '2', '3', '+'},
  {'4', '5', '6', '-'},
  {'7', '8', '9', 'C'},
  {'*', '0', '=', '/'}
};

byte pin_rows[ROW_NUM] = {9, 8, 7, 6}; // Connect keypad rows to pins 9, 8, 7, 6
byte pin_cols[COL_NUM] = {5, 4, 3, 2}; // Connect keypad columns to pins 5, 4, 3, 2

Keypad keypad = Keypad(makeKeymap(keys), pin_rows, pin_cols, ROW_NUM, COL_NUM);

// Variables for storing numbers and the result
float num1 = 0;
float num2 = 0;
char op = '\0';
float result = 0;

void setup() {
  // Initialize LCD and set the cursor
  lcd.init();  // Initialize the LCD
  lcd.backlight();  // Turn on the backlight
  lcd.setCursor(0, 0);
  lcd.print("Calculator");
  delay(2000);  // Display "Calculator" for 2 seconds
  lcd.clear();  // Clear the LCD screen
}

void loop() {
  char key = keypad.getKey();
  
  if (key) {
    lcd.clear();
    lcd.setCursor(0, 0);
    
    if (key == 'C') {
      num1 = num2 = result = 0;
      op = '\0';
      lcd.print("Cleared");
    } 
    else if (key == '=') {
      // Perform the calculation based on the operator
      switch (op) {
        case '+': result = num1 + num2; break;
        case '-': result = num1 - num2; break;
        case '*': result = num1 * num2; break;
        case '/': 
          if (num2 != 0) result = num1 / num2;
          else lcd.print("Error");
          break;
        default: 
          lcd.print("Invalid Op");
          return;
      }
      
      // Display result: Check if it's a whole number or not
      lcd.setCursor(0, 1);
      lcd.print("Result: ");
      
      if (result == (int)result) {
        // If result is an integer, display it as an integer
        lcd.print((int)result);  
      } else {
        // Otherwise, display it as a float
        lcd.print(result);
      }
      
      delay(2000);  // Display result for 2 seconds
      lcd.clear();  // Clear the LCD screen
      num1 = result;
      num2 = 0;
      op = '\0';
    }
    else if (key == '+' || key == '-' || key == '*' || key == '/') {
      op = key;
      lcd.print(op);
    } 
    else {
      // Check if key is a number
      if (op == '\0') {
        num1 = num1 * 10 + (key - '0');
        lcd.print(num1);
      } 
      else {
        if (key >= '0' && key <= '9') {
          num2 = num2 * 10 + (key - '0');
          lcd.print(num2);
        }
      }
    }
  }
}
