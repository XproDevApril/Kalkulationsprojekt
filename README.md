
// # Kalkulationsprojekt
// ## Arduino Uno-Projekt
// ## importar librerias, debe buscarlas
// Suerte

#include <LiquidCrystal.h>
#include <Keypad.h>

LiquidCrystal lcd(8, 9, 4, 5, 6, 7); // Configura los pines para la pantalla LCD
Keypad keypad = Keypad(makeKeymap("1234567890*-/+=C"), A0, A1, A2, A3); // Configura el teclado de membrana

char input[16] = "";
char operand1[16] = "";
char operand2[16] = "";
char operation = 0;
boolean decimalPoint = false;

void setup() {
  lcd.begin(16, 2); // Inicializa la pantalla LCD (16 columnas x 2 filas)
  lcd.setCursor(0, 0);
  lcd.print("Calculadora Arduino");
  lcd.setCursor(0, 1);
  lcd.print("Ingrese operacion");
}

void loop() {
  char key = keypad.getKey();
  if (key != NO_KEY) {
    if (key == 'C') {
      clearInput();
    } else if (key == '=') {
      calculate();
    } else {
      addToInput(key);
    }
  }
  updateDisplay();
}

void addToInput(char key) {
  if (strlen(input) < 16) {
    if (key >= '0' && key <= '9') {
      strcat(input, &key);
    } else if (key == '.' && !decimalPoint) {
      strcat(input, ".");
      decimalPoint = true;
    } else if (key == '+' || key == '-' || key == '*' || key == '/') {
      if (operation == 0) {
        strcpy(operand1, input);
        operation = key;
        clearInput();
      }
    }
  }
}

void clearInput() {
  strcpy(input, "");
  decimalPoint = false;
}

void calculate() {
  if (operation && strlen(operand1) > 0 && strlen(input) > 0) {
    float num1 = atof(operand1);
    float num2 = atof(input);
    float result = 0;

    switch (operation) {
      case '+':
        result = num1 + num2;
        break;
      case '-':
        result = num1 - num2;
        break;
      case '*':
        result = num1 * num2;
        break;
      case '/':
        if (num2 != 0) {
          result = num1 / num2;
        } else {
          lcd.clear();
          lcd.print("Error: Div/0");
          delay(2000);
          clearInput();
          return;
        }
        break;
    }

    dtostrf(result, 8, 2, input);
    operation = 0;
    strcpy(operand1, input);
    clearInput();
  }
}

void updateDisplay() {
  lcd.setCursor(0, 1);
  lcd.print("                ");
  lcd.setCursor(0, 1);
  lcd.print(input);
}
