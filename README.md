# PROYECTO-FINAL-PROGRAMACI-N
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include <time.h>
#include <windows.h>

#define MAX_VEHICULOS 100

#ifdef _WIN32
#define CLEAR "cls"
#else
#define CLEAR "clear"
#endif

// --- Definición de la estructura Vehiculo ---
typedef struct {
	char propietario[50];
	char placa[10];
	int anio;
	char tipo[20];
	float avaluo;
	int revisiones;
} Vehiculo;

// --- Prototipos ---
void inicializarVehiculos(Vehiculo vehiculos[], int len);
void registrarVehiculo(Vehiculo vehiculos[], int *numVehiculos);
void mostrarVehiculo(const Vehiculo *v);
void mostrarTodosVehiculos(const Vehiculo vehiculos[], int numVehiculos);
void pausar();
void mostrarDatos(char propietario[], char placa[], float valorBase, int anio);
void calcularMatricula(float *valorBase, int anio);
int validarNombre(char *nombre);
int validarPlaca(char *placa);
void registrarError(const char *mensaje);
void limpiarPantalla();
void esperar();


void inicializarVehiculos(Vehiculo vehiculos[], int len) {
	for (int i = 0; i < len; i++) {
		strcpy(vehiculos[i].propietario, "");
		strcpy(vehiculos[i].placa, "");
		vehiculos[i].anio = 0;
		strcpy(vehiculos[i].tipo, "");
		vehiculos[i].avaluo = 0.0f;
		vehiculos[i].revisiones = 0;
	}
}


void registrarVehiculo(Vehiculo vehiculos[], int *numVehiculos) {
	if (*numVehiculos >= MAX_VEHICULOS) {
		limpiarPantalla();
		printf("¡Capacidad máxima alcanzada!\n");
		registrarError("Error: Capacidad máxima de vehículos alcanzada.");
		esperar();
		return;
	}
	
	Vehiculo nuevo;
	
	// Nombre
	do {
		printf("Ingrese el nombre del propietario: ");
		fgets(nuevo.propietario, sizeof(nuevo.propietario), stdin);
		nuevo.propietario[strcspn(nuevo.propietario, "\n")] = '\0';
		if (!validarNombre(nuevo.propietario)) {
			limpiarPantalla();
			printf("Error: El nombre no debe contener números.\n");
			registrarError("Error: Nombre con números.");
			esperar();
			system(CLEAR);
		} else {
			break;
		}
	} while (1);
	
	// Placa
	do {
		printf("Ingrese la placa del vehículo: ");
		scanf("%s", nuevo.placa);
		if (!validarPlaca(nuevo.placa)) {
			limpiarPantalla();
			printf("Error: Placa inválida.\n");
			registrarError("Error: Placa inválida ingresada.");
			esperar();
		} else {
			break;
		}
	} while (1);
	
	// Año
	do {
		printf("Ingrese el año del vehículo (1900-2025): ");
		if (scanf("%d", &nuevo.anio) != 1 || nuevo.anio < 1900 || nuevo.anio > 2025) {
			limpiarPantalla();
			printf("Error: Año fuera de rango o inválido.\n");
			registrarError("Error: Año inválido.");
			while (getchar() != '\n');
			esperar();
		} else {
			break;
		}
	} while (1);
	
	// Tipo
	printf("Ingrese tipo de vehículo (e.g., Automóvil, Moto): ");
	scanf("%s", nuevo.tipo);
	
	// Avalúo
	do {
		printf("Ingrese avalúo base (>0): ");
		if (scanf("%f", &nuevo.avaluo) != 1 || nuevo.avaluo <= 0) {
			limpiarPantalla();
			printf("Error: Avalúo inválido.\n");
			registrarError("Error: Avalúo inválido.");
			while (getchar() != '\n');
			esperar();
		} else {
			break;
		}
	} while (1);
	
	while (getchar() != '\n'); // limpiar buffer
	
	calcularMatricula(&nuevo.avaluo, nuevo.anio);
	nuevo.revisiones = 0;
	
	vehiculos[*numVehiculos] = nuevo;
	(*numVehiculos)++;
	
	printf("\nVehículo registrado exitosamente.\n");
	mostrarDatos(nuevo.propietario, nuevo.placa, nuevo.avaluo, nuevo.anio);
	printf("Total a pagar por matrícula: %.2f\n", nuevo.avaluo);
}

void mostrarVehiculo(const Vehiculo *v) {
	printf("\n--- Datos del Vehículo ---\n");
	printf("Propietario: %s\n", v->propietario);
	printf("Placa: %s\n", v->placa);
	printf("Año: %d\n", v->anio);
	printf("Tipo: %s\n", v->tipo);
	printf("Avalúo: %.2f\n", v->avaluo);
	printf("Revisiones: %d\n", v->revisiones);
}

void mostrarTodosVehiculos(const Vehiculo vehiculos[], int numVehiculos) {
	if (numVehiculos == 0) {
		printf("No hay vehículos registrados.\n");
		return;
	}
	
	for (int i = 0; i < numVehiculos; i++) {
		mostrarVehiculo(&vehiculos[i]);
	}
}

void mostrarDatos(char propietario[], char placa[], float valorBase, int anio) {
	printf("\n--- DATOS DEL VEHICULO ---\n");
	printf("Propietario: %s\n", propietario);
	printf("Placa: %s\n", placa);
	printf("Año: %d\n", anio);
	printf("Valor base: %.2f\n", valorBase);
}

void calcularMatricula(float *valorBase, int anio) {
	if (anio < 2015) {
		*valorBase *= 0.85;
	} else if (anio <= 2022) {
		*valorBase *= 0.95;
	}
}

int validarNombre(char *nombre) {
	for (int i = 0; nombre[i] != '\0'; i++) {
		if (isdigit((unsigned char)nombre[i])) return 0;
	}
	return 1;
}

int validarPlaca(char *placa) {
	int len = strlen(placa);
	if (len > 8 || len == 0) return 0;
	for (int i = 0; i < len; i++) {
		if (!isalnum(placa[i]) && placa[i] != '-') return 0;
	}
	return 1;
}

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

void pausar() {
	printf("\nPresione Enter para volver al menú...");
	getchar();
}

void limpiarPantalla() {
	system(CLEAR);
}

void esperar() {
#ifdef _WIN32
	Sleep(3000);
#else
	sleep(2);
#endif
}

int main() {
	Vehiculo vehiculos[MAX_VEHICULOS];
	int numVehiculos = 0, opcion;
	
	inicializarVehiculos(vehiculos, MAX_VEHICULOS);
	
	do {
		system(CLEAR);
		printf("\n--- Sistema de Matriculación Vehicular ---\n");
		printf("1. Registrar nuevo vehículo\n");
		printf("2. Mostrar todos los vehículos registrados\n");
		printf("3. Salir\n");
		printf("Seleccione una opción: ");
		
		if (scanf("%d", &opcion) != 1) {
			limpiarPantalla();
			printf("Entrada inválida. Intente nuevamente.\n");
			registrarError("Error: Entrada inválida en el menú principal.");
			while (getchar() != '\n');
			esperar();
			continue;
		}
		
		while (getchar() != '\n'); // limpiar buffer
		system(CLEAR);
		
		switch (opcion) {
		case 1:
			registrarVehiculo(vehiculos, &numVehiculos);
			break;
		case 2:
			mostrarTodosVehiculos(vehiculos, numVehiculos);
			break;
		case 3:
			printf("Saliendo del sistema...\n");
			break;
		default:
			limpiarPantalla();
			printf("Opción inválida. Intente nuevamente.\n");
			registrarError("Error: Opción inválida seleccionada.");
			esperar();
		}
		
		if (opcion != 3) pausar();
		system(CLEAR);
	} while (opcion != 3);
	
	return 0;
}


