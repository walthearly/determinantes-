# determinantes-
algebra lineal, aplicativo de determinantes. 
import torch

def solicitar_tamano():
    """
    Solicita el tamaño del sistema (n x n).
    """
    while True:
        try:
            n = int(input("Ingrese el número de variables del modelo económico (n ≥ 2): "))
            if n < 2:
                print("Error: Debe ingresar al menos 2 variables.")
            else:
                return n
        except ValueError:
            print("Entrada inválida. Intente nuevamente.")

def solicitar_matriz(n):
    """
    Solicita los coeficientes de la matriz A.
    """
    print("\nIngrese los coeficientes de la matriz A (una fila por línea):")
    matriz = []
    for i in range(n):
        while True:
            entrada = input(f"  Fila {i+1}: ").strip().split()
            if len(entrada) != n:
                print(f"  Error: Debe ingresar exactamente {n} números.")
                continue
            try:
                fila = [float(x) for x in entrada]
                matriz.append(fila)
                break
            except ValueError:
                print("  Error: Solo se permiten valores numéricos.")
    return torch.tensor(matriz, dtype=torch.float64)

def solicitar_vector(n):
    """
    Solicita el vector b (resultados económicos deseados).
    """
    print("\nIngrese el vector de resultados b (uno por línea):")
    vector = []
    for i in range(n):
        while True:
            try:
                valor = float(input(f"  Resultado {i+1}: "))
                vector.append(valor)
                break
            except ValueError:
                print("  Error: Ingrese un valor numérico.")
    return torch.tensor(vector, dtype=torch.float64).reshape(-1, 1)

def mostrar_sistema(A, b):
    """
    Imprime el sistema de ecuaciones económicas en formato legible.
    """
    print("\nModelo económico:")
    n = A.shape[0]
    for i in range(n):
        ecuacion = " + ".join(f"{A[i,j].item():.2f}·x{j+1}" for j in range(n))
        print(f"  {ecuacion} = {b[i].item():.2f}")

def calcular_determinante(A):
    """
    Calcula y retorna el determinante de A.
    """
    return torch.det(A)

def interpretar_determinante(det, tolerancia=1e-6):
    """
    Interpreta el valor del determinante.
    """
    print(f"\nDeterminante: {det.item():.6f}")
    if abs(det.item()) < tolerancia:
        print("Conclusión: El modelo es INVIABLE. No tiene solución única.")
        return False
    else:
        print("Conclusión: El modelo es VIABLE. Existe una solución única.")
        return True

def resolver_sistema(A, b):
    """
    Resuelve el sistema si es viable.
    """
    x = torch.linalg.solve(A, b)
    print("\nSolución del modelo (variables económicas):")
    for i, valor in enumerate(x):
        print(f"  x{i+1} = {valor.item():.4f}")
    return x

def interpretar_resultado(x):
    """
    Proporciona interpretación general de los valores obtenidos.
    """
    print("\nInterpretación general:")
    for i, valor in enumerate(x):
        if valor.item() < 0:
            print(f"  Advertencia: x{i+1} es negativo. Puede representar una pérdida o desajuste.")
        else:
            print(f"  x{i+1} es positivo. Valor aceptable en contexto económico.")

def main():
    print("="*70)
    print("   MODELO DE VIABILIDAD ECONÓMICA USANDO DETERMINANTES DE LAS MATRICES")
    print("="*70)
    print("Este programa evalúa un modelo económico basado en relaciones lineales\n")

    n = solicitar_tamano()
    A = solicitar_matriz(n)
    b = solicitar_vector(n)

    mostrar_sistema(A, b)

    det = calcular_determinante(A)
    es_viable = interpretar_determinante(det)

    if es_viable:
        x = resolver_sistema(A, b)
        interpretar_resultado(x)
    else:
        print("\nSugerencia: Revise los datos del modelo. Puede haber variables redundantes o mal formuladas.")

if __name__ == "__main__":
    main()

