# PROYECTO-FINAL-PROGRAMACI-N
Proyecto en grupo de la asignatura de programación 
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include <time.h> // ? Para manejar fecha y hora

// Función para mostrar los datos
void mostrarDatos(char propietario[], float valorBase, int anio) {
	printf("\n--- DATOS DEL VEHICULO ---\n");
	printf("Propietario: %s", propietario);
	printf("Año: %d\n", anio);
	printf("Valor base: %.2f\n", valorBase);
}

// Función para calcular la matrícula
void calcularMatricula(float *valorBase, int anio) {
	if (anio < 2015) {
		*valorBase *= 0.85;
	} else if (anio <= 2022) {
		*valorBase *= 0.95;
	}
}

// Función para validar que el nombre no contenga números
int validarNombre(char *nombre) {
	for (int i = 0; nombre[i] != '\0'; i++) {
		if (isdigit((unsigned char)nombre[i])) {
			return 0;
		}
	}
	return 1;
}

// ?? Función para registrar errores con fecha y hora
void registrarError(const char *mensaje) {
	FILE *log = fopen("errores.log", "a");
	if (log != NULL) {
		time_t ahora = time(NULL);
		struct tm *tinfo = localtime(&ahora);
		char fechaHora[100];
		
		strftime(fechaHora, sizeof(fechaHora), "[%Y-%m-%d %H:%M:%S]", tinfo);
		fprintf(log, "%s %s\n", fechaHora, mensaje);
		fclose(log);
	}
}

int main() {
	char propietario[50];
	float valorBase;
	int anio;
	
	// Ingreso y validación del nombre del propietario
	printf("Ingrese el nombre del propietario: ");
	fgets(propietario, sizeof(propietario), stdin);
	
	if (!validarNombre(propietario)) {
		printf("Error: El nombre no debe contener numeros.\n");
		registrarError("Error: El usuario ingreso numeros en el nombre del propietario.");
		return 1;
	}
	
	// Ingreso y validación del año
	printf("Ingrese el año del vehiculo: ");
	if (scanf("%d", &anio) != 1) {
		printf("Error: Debe ingresar un número entero válido para el año.\n");
		registrarError("Error: Anio del vehiculo ingresado no es un numero valido.");
		return 1;
	}
	
	// Ingreso y validación del valor base
	printf("Ingrese el valor base de la matricula: ");
	if (scanf("%f", &valorBase) != 1) {
		printf("Error: Debe ingresar un número valido para el valor de la matrícula.\n");
		registrarError("Error: Valor base de matricula no valido.");
		return 1;
	}
	
	mostrarDatos(propietario, valorBase, anio);
	calcularMatricula(&valorBase, anio);
	printf("\n--- VALOR ACTUALIZADO DE MATRICULA ---\n");
	printf("Total a pagar: %.2f\n", valorBase);
	
	return 0;
}
