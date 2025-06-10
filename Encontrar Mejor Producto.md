## Medida para obtener el nombre del producto o cualquier dimensión con el valor más alto dentro de una categoria
```
ProductoMasVendidoPorCategoria = 
CALCULATE (
    SELECTEDVALUE ( D_productos[Producto] ),
    INDEX (
        1,
        ALLSELECTED ( D_productos[Producto] ),
        ORDERBY ( 'Medidas Ventas'[ventas], DESC )
    )
)

// Extraer el valor de ese producto más vendido
$ProductoMasVendidoPorCategoria = 
CALCULATE (
    'Medidas Ventas'[ventas],
    INDEX (
        1,
        ALLSELECTED ( D_productos[Producto] ),
        ORDERBY ( 'Medidas Ventas'[ventas], DESC )
    )
)

// Sacar la participación en cuanto a la venta total
%Parti/Pdto =
COALESCE (
    DIVIDE ( [$ProductoMasVendidoPorCategoria], 'Medidas Ventas'[ventas], 0 ),
    0
)
```
