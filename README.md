// proyecto_1B.cpp
//

#include <iostream>
#include<stdio.h>
#include<stdlib.h>
#include <conio.h>
#include <cctype>
#include <cstdlib>
#include <string.h>
#include <time.h>
#include <string.h>
#include <math.h>
#include <sstream>
#include <random>

using namespace std;


//-------------


/*
PADRON CARGADO
*/
typedef struct T_Votante { //Se define la estructura para los votantes
	char cedula[10];
	char codelec[7];
	char sexo;
	char fecha[9];
	char numjun[6];
	char nombre[31];
	char papellido[27];
	char sapellido[27];
	T_Votante* PtrSiguiente;
}*PtrT_Votante;

//Funcion para agregar un votante, recibe como parametro la lista de votantes y la LINEA DE TEXTO del padron electoral
//extrae cada uno de los atributos de la linea y los almacena en un nodo de la lista de votantes
void agregarvotante(PtrT_Votante& ListaV, char agregado[118]) {
	PtrT_Votante Aux = new (T_Votante);
	Aux->PtrSiguiente = ListaV;
	ListaV = Aux;

	for (int ce = 0; ce < 9; ce++) {
		ListaV->cedula[ce] = agregado[ce];
	}
	ListaV->cedula[9] = '\0';

	for (int co = 10; co < 16; co++) {
		ListaV->codelec[co - 10] = agregado[co];
	}
	ListaV->codelec[6] = '\0';

	ListaV->sexo = agregado[17];


	for (int fe = 19; fe < 27; fe++) {
		ListaV->fecha[fe - 19] = agregado[fe];
	}
	ListaV->fecha[8] = '\0';

	for (int nu = 28; nu < 33; nu++) {
		ListaV->numjun[nu - 28] = agregado[nu];
	}
	ListaV->numjun[5] = '\0';

	for (int nom = 34; nom < 64; nom++) {
		ListaV->nombre[nom - 34] = agregado[nom];
	}
	ListaV->nombre[30] = '\0';

	for (int pa = 65; pa < 91; pa++) {
		ListaV->papellido[pa - 65] = agregado[pa];
	}
	ListaV->papellido[26] = '\0';

	for (int sa = 92; sa < 118; sa++) {
		ListaV->sapellido[sa - 92] = agregado[sa];
	}
	ListaV->sapellido[26] = '\0';

}



//Funcion para cargar los votantes, se encarga de tomar cada una de las lineas
//directo del archivo de texto y enviarlo a la funci�n encargada de extraer cad uno
// de los datos e incorporarlos en un nodo de la lista.
void CargarVotantes(PtrT_Votante& ListaV) {
	int cont = 1;
	time_t inicio, fin;
	inicio = time(NULL);
	FILE* archivo;
	fopen_s(&archivo, "PADRON_COMPLETO.txt", "r");
	if (NULL == archivo) {
		printf("No se pudo abrir el archivo");
	}
	else {
		char agregado[119];
		while (fgets(agregado, 119, archivo) != NULL) {
			if (cont % 2 != 0) {
				agregarvotante(ListaV, agregado);
			}
			cont++;
		}
		fclose(archivo);
	}

	fin = time(NULL);
	cout << "----------------------------Padron cargado--------------------------------" << endl;
	printf("\nEl cargado del padron ha tardado : %f segundos.\n", difftime(fin, inicio));
	cout << "----------------------------Padron cargado--------------------------------" << endl;
	system("pause");
}


//Funcion para liberar los votantes en estructuras de memoria dinamica de la lista enlazada hasta dejar la lista en NULL

void LiberarVotantes(PtrT_Votante& ListaV) {
	PtrT_Votante Aux = ListaV;
	time_t inicio, fin;

	inicio = time(NULL);

	while (Aux != NULL) {
		ListaV = ListaV->PtrSiguiente;
		delete(Aux);
		//Globalfree(Aux);
		Aux = ListaV;
	}

	fin = time(NULL);

	cout << "----------------------------Padron liberado--------------------------------" << endl;
	printf("\nLa liberacion del padron ha tardado : %f segundos.\n", difftime(fin, inicio));
	cout << "----------------------------Padron liberado--------------------------------" << endl;
	system("pause");
}


//Funci�n para inicializar la lista de votantes
void inicializarvotantes(PtrT_Votante& ListaV) {
	ListaV = NULL;
}

PtrT_Votante BuscarVotante(PtrT_Votante& Lista, char cual[9])
{
	bool encontro = false;
	PtrT_Votante Aux;
	Aux = Lista;

	while ((!encontro == true) && (Aux != NULL))
	{
		if (strncmp(Aux->cedula, cual, 9) == 0)
			encontro = true;
		else
			Aux = Aux->PtrSiguiente;
	}
	return Aux;
}


/*
Arbol Binario de Busqueda
*/

typedef struct NodoBB
{
	int llave_ABB;
	NodoBB* HijoIzquierdo;
	NodoBB* HijoDerecho;
}X;

bool Insertar_ABB(NodoBB*& Raiz, int cualllave)
{
	if (Raiz == NULL)
	{
		Raiz = new(NodoBB);
		Raiz->llave_ABB = cualllave;
		Raiz->HijoIzquierdo = NULL;
		Raiz->HijoDerecho = NULL;
		return true;
	}
	else
	{
		if (cualllave == Raiz->llave_ABB) return false;
		else
			if (cualllave < Raiz->llave_ABB) return Insertar_ABB(Raiz->HijoIzquierdo, cualllave);
			else return Insertar_ABB(Raiz->HijoDerecho, cualllave);
	}
}

void PodarHojas(NodoBB*& Raiz)
{
	if (Raiz != NULL)
	{
		PodarHojas(Raiz->HijoIzquierdo);
		PodarHojas(Raiz->HijoDerecho);
		printf("Borro : %i \n", Raiz->llave_ABB);
		delete(Raiz); Raiz = NULL;
	}
}

NodoBB* Buscar(NodoBB* Raiz, int cualllave)
{
	if (Raiz == NULL)
	{
		return NULL;
	}
	else
	{
		if (cualllave == Raiz->llave_ABB) return Raiz;
		else
			if (cualllave < Raiz->llave_ABB) return Buscar(Raiz->HijoIzquierdo, cualllave);
			else return Buscar(Raiz->HijoDerecho, cualllave);
	}
}

void PreOrdenRID(NodoBB* Raiz)
{
	if (Raiz != NULL)
	{
		printf("%i  ", Raiz->llave_ABB);
		PreOrdenRID(Raiz->HijoIzquierdo);
		PreOrdenRID(Raiz->HijoDerecho);
	}
}

void EnOrdenIRD(NodoBB* Raiz)
{
	if (Raiz != NULL)
	{
		EnOrdenIRD(Raiz->HijoIzquierdo);
		printf("%i  ", Raiz->llave_ABB);
		EnOrdenIRD(Raiz->HijoDerecho);
	}
}

void PosOrdenIDR(NodoBB* Raiz)
{
	if (Raiz != NULL)
	{
		PosOrdenIDR(Raiz->HijoIzquierdo);
		PosOrdenIDR(Raiz->HijoDerecho);
		printf("%i  ", Raiz->llave_ABB);
	}
}


/*
BTree
*/

#define MAX 4
#define MIN 2

struct NodoBtree {
	int val[MAX + 1], count;
	NodoBtree* link[MAX + 1];
};

NodoBtree* raiz;

//Crear nodo 
NodoBtree* crearnodo(int val, NodoBtree* hijo) {
	NodoBtree* NuevoNodo = new NodoBtree;
	NuevoNodo->val[1] = val;
	NuevoNodo->count = 1;
	NuevoNodo->link[0] = raiz;
	NuevoNodo->link[1] = hijo;
	return NuevoNodo;
}

//coloca el nodo en la posicion adecuada, de acuerdo a su valor
void Colocarnodo(int val, int pos, NodoBtree* node, NodoBtree* hijo) {
	int j = node->count;
	while (j > pos) {
		node->val[j + 1] = node->val[j];
		node->link[j + 1] = node->link[j];
		j--;
	}
	node->val[j + 1] = val;
	node->link[j + 1] = hijo;
	node->count++;
}


void PartirNodo(int val, int* pval, int pos, NodoBtree* node, NodoBtree* hijo, NodoBtree** NuevoNodo) {
	int median, j;

	if (pos > MIN)
		median = MIN + 1;
	else
		median = MIN;

	*NuevoNodo = new NodoBtree;
	j = median + 1;
	while (j <= MAX) {
		(*NuevoNodo)->val[j - median] = node->val[j];
		(*NuevoNodo)->link[j - median] = node->link[j];
		j++;
	}
	node->count = median;
	(*NuevoNodo)->count = MAX - median;

	if (pos <= MIN) {
		Colocarnodo(val, pos, node, hijo);
	}
	else {
		Colocarnodo(val, pos - median, *NuevoNodo, hijo);
	}
	*pval = node->val[node->count];
	(*NuevoNodo)->link[0] = node->link[node->count];
	node->count--;
}

//colocar valor
int SetValorNodo(int val, int* pval, NodoBtree* node, NodoBtree** hijo) {

	int pos;
	if (!node) {
		*pval = val;
		*hijo = NULL;
		return 1;
	}

	if (val < node->val[1]) {
		pos = 0;
	}
	else {
		for (pos = node->count;
			(val < node->val[pos] && pos > 1); pos--);
		if (val == node->val[pos]) {
			cout << "No está permitido nodos duplicados\n";
			return 0;
		}
	}
	if (SetValorNodo(val, pval, node->link[pos], hijo)) {
		if (node->count < MAX) {
			Colocarnodo(*pval, pos, node, *hijo);
		}
		else {
			PartirNodo(*pval, pval, pos, node, *hijo, hijo);
			return 1;
		}
	}
	return 0;
}

//inserta valores en el btree
void insertar(int val) {
	int flag, i;
	NodoBtree* hijo;

	flag = SetValorNodo(val, &i, raiz, &hijo);
	if (flag)
		raiz = crearnodo(i, hijo);
}

//copia el sucesor del nodo que será borrado
void copySuccessor(NodoBtree* minodo, int pos) {
	NodoBtree* dummy;
	dummy = minodo->link[pos];

	for (; dummy->link[0] != NULL;)
		dummy = dummy->link[0];
	minodo->val[pos] = dummy->val[1];

}

//remueve un valor de un nodo y reacomoda el arbol
void removeVal(NodoBtree* minodo, int pos) {
	int i = pos + 1;
	while (i <= minodo->count) {
		minodo->val[i - 1] = minodo->val[i];
		minodo->link[i - 1] = minodo->link[i];
		i++;
	}
	minodo->count--;
}

//shift a la derecha
void doRightShift(NodoBtree* minodo, int pos) {
	NodoBtree* x = minodo->link[pos];
	int j = x->count;

	while (j > 0) {
		x->val[j + 1] = x->val[j];
		x->link[j + 1] = x->link[j];
	}
	x->val[1] = minodo->val[pos];
	x->link[1] = x->link[0];
	x->count++;

	x = minodo->link[pos - 1];
	minodo->val[pos] = x->val[x->count];
	minodo->link[pos] = x->link[x->count];
	x->count--;
	return;
}

//shift a la izquierda
void doLeftShift(NodoBtree* minodo, int pos) {
	int j = 1;
	NodoBtree* x = minodo->link[pos - 1];

	x->count++;
	x->val[x->count] = minodo->val[pos];
	x->link[x->count] = minodo->link[pos]->link[0];

	x = minodo->link[pos];
	minodo->val[pos] = x->val[1];
	x->link[0] = x->link[1];
	x->count--;

	while (j <= x->count) {
		x->val[j] = x->val[j + 1];
		x->link[j] = x->link[j + 1];
		j++;
	}
	return;
}

//Fusionar nodos
void UnirNodos(NodoBtree* minodo, int pos) {
	int j = 1;
	NodoBtree* x1 = minodo->link[pos], * x2 = minodo->link[pos - 1];

	x2->count++;
	x2->val[x2->count] = minodo->val[pos];
	x2->link[x2->count] = minodo->link[0];

	while (j <= x1->count) {
		x2->count++;
		x2->val[x2->count] = x1->val[j];
		x2->link[x2->count] = x1->link[j];
		j++;
	}

	j = pos;
	while (j < minodo->count) {
		minodo->val[j] = minodo->val[j + 1];
		minodo->link[j] = minodo->link[j + 1];
		j++;
	}
	minodo->count--;
	free(x1);
}

//Ajusta el nodo dado
void AjustarNodo(NodoBtree* minodo, int pos) {
	if (!pos) {
		if (minodo->link[1]->count > MIN) {
			doLeftShift(minodo, 1);
		}
		else {
			UnirNodos(minodo, 1);
		}
	}
	else {
		if (minodo->count != pos) {
			if (minodo->link[pos - 1]->count > MIN) {
				doRightShift(minodo, pos);
			}
			else {
				if (minodo->link[pos + 1]->count > MIN) {
					doLeftShift(minodo, pos + 1);
				}
				else {
					UnirNodos(minodo, pos);
				}
			}
		}
		else {
			if (minodo->link[pos - 1]->count > MIN)
				doRightShift(minodo, pos);
			else
				UnirNodos(minodo, pos);
		}
	}
}

//borrar un valor del nodo
int BorrarDeNodo(int val, NodoBtree* minodo) {
	int pos, flag = 0;
	if (minodo) {
		if (val < minodo->val[1]) {
			pos = 0;
			flag = 0;
		}
		else {
			for (pos = minodo->count;
				(val < minodo->val[pos] && pos > 1); pos--);
			if (val == minodo->val[pos]) {
				flag = 1;
			}
			else {
				flag = 0;
			}
		}
		if (flag) {
			if (minodo->link[pos - 1]) {
				copySuccessor(minodo, pos);
				flag = BorrarDeNodo(minodo->val[pos], minodo->link[pos]);
				if (flag == 0) {
					cout << "No existe en el B-Tree\n";
				}
			}
			else {
				removeVal(minodo, pos);
			}
		}
		else {
			flag = BorrarDeNodo(val, minodo->link[pos]);
		}
		if (minodo->link[pos]) {
			if (minodo->link[pos]->count < MIN)
				AjustarNodo(minodo, pos);
		}
	}
	return flag;
}

/* delete val from B-tree */
void borrado(int val, NodoBtree* minodo) {
	NodoBtree* tmp;
	if (!BorrarDeNodo(val, minodo)) {
		cout << "No existe ese valor en el B-Tree\n";
		system("pause");
		return;
	}
	else {
		if (minodo->count == 0) {
			tmp = minodo;
			minodo = minodo->link[0];
			free(tmp);
		}
	}
	raiz = minodo;
	cout << "Borrado" << endl;
	system("pause");
	return;
}

/* search val in B-Tree */
void busqueda(int val, int* pos, NodoBtree* minodo) {
	if (!minodo) {
		return;
	}

	if (val < minodo->val[1]) {
		*pos = 0;
	}
	else {
		for (*pos = minodo->count;
			(val < minodo->val[*pos] && *pos > 1); (*pos)--);
		if (val == minodo->val[*pos]) {
			cout << "Se ha encontrado el Nodo\n";
			system("pause");
			return;
		}
	}
	busqueda(val, pos, minodo->link[*pos]);
	cout << "No se ha encontrado el Nodo\n";
	system("pause");
	return;
}

// B-Tree desplegar
void desplegar(NodoBtree* minodo) {
	int i;
	if (minodo) {
		for (i = 0; i < minodo->count; i++) {
			desplegar(minodo->link[i]);
			cout << minodo->val[i + 1] << ' ';
		}
		desplegar(minodo->link[i]);
	}
}

/*
------------ ARBOL AVL -----------------------------------------------------------------------------------------------------------------------------------------------------------------
*/

struct Nodo
{
	int llave;
	struct Nodo* izq;
	struct Nodo* derecha;
	int altura;
};

//obtiene el maximo de dos numeros
int max(int a, int b);

//obtiene la altura del arbol
int altura(struct Nodo* N)
{
	if (N == NULL)
		return 0;
	return N->altura;
}


int max(int a, int b)
{
	return (a > b) ? a : b;
}


struct Nodo* nuevoNodo(int llave)
{
	struct Nodo* Nodo = (struct Nodo*)
		malloc(sizeof(struct Nodo));
	Nodo->llave = llave;
	Nodo->izq = NULL;
	Nodo->derecha = NULL;
	Nodo->altura = 1;  //nuevonodo es agregado como hoja
	return(Nodo);
}


struct Nodo* RotarDerecha(struct Nodo* y)
{
	struct Nodo* x = y->izq;
	struct Nodo* T2 = x->derecha;

	// hace rotacion
	x->derecha = y;
	y->izq = T2;

	// actualiza alturas
	y->altura = max(altura(y->izq), altura(y->derecha)) + 1;
	x->altura = max(altura(x->izq), altura(x->derecha)) + 1;

	// retorna nueva raiz
	return x;
}


struct Nodo* RotarIzquierda(struct Nodo* x)
{
	struct Nodo* y = x->derecha;
	struct Nodo* T2 = y->izq;

	// hace rotacion
	y->izq = x;
	x->derecha = T2;

	//  actualiza alturas
	x->altura = max(altura(x->izq), altura(x->derecha)) + 1;
	y->altura = max(altura(y->izq), altura(y->derecha)) + 1;

	// retorna nueva raiz
	return y;
}

// obtiene balance
int obtenerbalance(struct Nodo* N)
{
	if (N == NULL)
		return 0;
	return altura(N->izq) - altura(N->derecha);
}


struct Nodo* insertarAVL(struct Nodo* Nodo, int llave)
{
	/* 1. Insercion normal */
	if (Nodo == NULL)
		return(nuevoNodo(llave));

	if (llave < Nodo->llave)
		Nodo->izq = insertarAVL(Nodo->izq, llave);
	else if (llave > Nodo->llave)
		Nodo->derecha = insertarAVL(Nodo->derecha, llave);
	else // no se permiten llaves iguales
		return Nodo;

	/* 2. actualiza altura */
	Nodo->altura = 1 + max(altura(Nodo->izq),
		altura(Nodo->derecha));

	/* 3. verifica si el arbol se desbalancea */
	int balance = obtenerbalance(Nodo);

	// 4 casos para un arbol desbalanceado

	// izq izq 
	if (balance > 1 && llave < Nodo->izq->llave)
		return RotarDerecha(Nodo);

	// derecha derecha 
	if (balance < -1 && llave > Nodo->derecha->llave)
		return RotarIzquierda(Nodo);

	// izq derecha 
	if (balance > 1 && llave > Nodo->izq->llave)
	{
		Nodo->izq = RotarIzquierda(Nodo->izq);
		return RotarDerecha(Nodo);
	}

	// derecha izq 
	if (balance < -1 && llave < Nodo->derecha->llave)
	{
		Nodo->derecha = RotarDerecha(Nodo->derecha);
		return RotarIzquierda(Nodo);
	}


	return Nodo;
}

//recorido
void preOrder(struct Nodo* root)
{
	if (root != NULL)
	{
		printf("%d ", root->llave);
		preOrder(root->izq);
		preOrder(root->derecha);
	}
}


int main() 
{
	int val, opt;
	struct Nodo* root = NULL;
	NodoBB* ArbolEnteros = NULL;

	while (true) {
		system("CLS");
		cout << "1. insertar\n";
		cout << "2. Borrar\n";
		cout << "3. Buscar\n";
		cout << "4. Desplegar (InOrder)\n";
		cout << "5. AVL Tree\n";
		cout << "6. Arbol Binario de Busqueda\n";
		cout << "7. Salir \n Opcion? ";
		cin >> opt;
		cout << endl;
		switch (opt) {
		case 1:
			cout << "Ingrese valor del nodo:";
			cin >> val;
			insertar(val);
			break;
		case 2:
			cout << "Cual elemento desea borrar:";
			cin >> val;
			borrado(val, raiz);
			break;
		case 3:
			cout << "Cual elemento desea buscar?";
			cin >> val;
			busqueda(val, &opt, raiz);
			break;
		case 4:
			desplegar(raiz);
			break;
		case 5:
			//construyendo el arbol siguiente
			root = insertarAVL(root, 10);
			root = insertarAVL(root, 20);
			root = insertarAVL(root, 30);
			root = insertarAVL(root, 40);
			root = insertarAVL(root, 50);
			root = insertarAVL(root, 25);

			/* The constructesd AVL Tree would be
			30
			/  \
			20   40
			/  \     \
			10  25    50
			*/

			printf("Preorder del AVL"
				" es \n");
			preOrder(root);

			return 0;
			break;
		case 6:
			printf("\nINSERTANDO la secuencia de Elementos : 9, 6, 16, 3, 13, 8, 18 ");
			Insertar_ABB(ArbolEnteros, 9);
			Insertar_ABB(ArbolEnteros, 6);
			Insertar_ABB(ArbolEnteros, 16);
			Insertar_ABB(ArbolEnteros, 3);
			Insertar_ABB(ArbolEnteros, 13);	
			Insertar_ABB(ArbolEnteros, 8);
			Insertar_ABB(ArbolEnteros, 18);

			printf("\n\nRECORIENDO: ");
			printf("\nElementos en recorrido PreOrden : ");
			PreOrdenRID(ArbolEnteros);

			printf("\nElementos en recorrido EnOrden : ");
			EnOrdenIRD(ArbolEnteros);

			printf("\nElementos en recorrido PosOrden : ");
			PosOrdenIDR(ArbolEnteros);

			printf("\n\nBUSCANDO 1 x 1 : ");
			NodoBB* Aux;
			if ((Aux = Buscar(ArbolEnteros, 18)) != NULL) printf("\nBusco %i y encontro %i ", 18, Aux->llave_ABB);
			if ((Aux = Buscar(ArbolEnteros, 8)) != NULL) printf("\nBusco %i y encontro %i ", 8, Aux->llave_ABB);
			if ((Aux = Buscar(ArbolEnteros, 13)) != NULL) printf("\nBusco %i y encontro %i ", 13, Aux->llave_ABB);
			if ((Aux = Buscar(ArbolEnteros, 3)) != NULL) printf("\nBusco %i y encontro %i ", 3, Aux->llave_ABB);
			if ((Aux = Buscar(ArbolEnteros, 16)) != NULL) printf("\nBusco %i y encontro %i ", 16, Aux->llave_ABB);
			if ((Aux = Buscar(ArbolEnteros, 6)) != NULL) printf("\nBusco %i y encontro %i ", 6, Aux->llave_ABB);
			if ((Aux = Buscar(ArbolEnteros, 9)) != NULL) printf("\nBusco %i y encontro %i ", 9, Aux->llave_ABB);

			printf("\n\nBORRANDO TODO ... de las hojas a la Raiz:\n");
			PodarHojas(ArbolEnteros);
			system("pause");
			break;
		case 7:
			exit(0);
		}
		cout << endl;
	}

	system("pause");
}
