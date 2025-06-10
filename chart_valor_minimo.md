-- Medida DAX para hallar el valor minimo en el tiempo
```
Minimo = 
VAR _Punto_Minimo = MINX(
  FILTER(
    ALLSELECTED( Calendario[Fecha] ) , 
    NOT ISBLANK( [Ventas] )), 
  [Ventas]
)

VAR _Resultado = 
IF( [Ventas] = _Punto_Minimo, [Ventas], BLANK() )
RETURN _Resultado
```

---
resultado
![image](https://github.com/user-attachments/assets/729d41f1-0e56-4a73-a53a-e428cad3d305)
