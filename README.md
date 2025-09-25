import { useState } from "react";
import { Card, CardContent, CardDescription, CardFooter, CardHeader, CardTitle } from "/components/ui/card";
import { Button } from "/components/ui/button";
import { Input } from "/components/ui/input";
import { Label } from "/components/ui/label";
import { RadioGroup, RadioGroupItem } from "/components/ui/radio-group";
import { Calendar } from "/components/ui/calendar";
import { Popover, PopoverContent, PopoverTrigger } from "/components/ui/popover";
import { CalendarIcon, Phone, Mail, MapPin } from "lucide-react";
import { format } from "date-fns";
import { es } from "date-fns/locale";

interface Product {
  id: number;
  name: string;
  price: number;
  category: string;
  description: string;
  image: string;
  alt: string;
}

interface CartItem extends Product {
  quantity: number;
}

const TiendaOnline = () => {
  const [cart, setCart] = useState<CartItem[]>([]);
  const [selectedDate, setSelectedDate] = useState<Date>();
  const [selectedTime, setSelectedTime] = useState("10:00");
  const [currentView, setCurrentView] = useState<"products" | "cart" | "schedule" | "confirmation">("products");
  const [orderConfirmed, setOrderConfirmed] = useState(false);

  const whatsappNumber = "5732026135"; // Número de WhatsApp formateado para Colombia (asumiendo 32026135 como local)

  const products: Product[] = [
    // Golosinas
    {
      id: 1,
      name: "Chocolates Variados",
      price: 45.00,
      category: "golosinas",
      description: "Mezcla de chocolates de leche, oscuro y blanco",
      image: "https://placeholder-image-service.onrender.com/image/300x200?prompt=Assortment%20of%20different%20chocolates%20in%20colorful%20wrappers&id=choco-varied",
      alt: "Variedad de chocolates con envolturas coloridas"
    },
    {
      id: 2,
      name: "Galletas de Chocolate",
      price: 32.50,
      category: "golosinas",
      description: "Galletas crujientes con trozos de chocolate",
      image: "https://placeholder-image-service.onrender.com/image/300x200?prompt=Chocolate%20chip%20cookies%20on%20a%20plate&id=cookies-chip",
      alt: "Galletas con chispas de chocolate sobre un plato"
    },
    {
      id: 3,
      name: "Gomitas de Fruta",
      price: 28.00,
      category: "golosinas",
      description: "Gomitas surtidas de sabores frutales",
      image: "https://placeholder-image-service.onrender.com/image/300x200?prompt=Colorful%20gummy%20candies%20in%20a%20bowl&id=gummies-fruit",
      alt: "Gomitas de colores variados en un tazón"
    },
    {
      id: 4,
      name: "Paletas de Caramelo",
      price: 15.00,
      category: "golosinas",
      description: "Paletas de caramelo con diversos sabores",
      image: "https://placeholder-image-service.onrender.com/image/300x200?prompt=Colorful%20lollipops%20on%20a%20display%20stand&id=lollipops-display",
      alt: "Paletas de caramelo coloridas en un expositor"
    },

    // Aguas y bebidas
    {
      id: 5,
      name: "Agua Mineral 1L",
      price: 18.00,
      category: "bebidas",
      description: "Agua mineral natural sin gas",
      image: "https://placeholder-image-service.onrender.com/image/300x200?prompt=Mineral%20water%20bottle%20with%20condensation&id=water-mineral",
      alt: "Botella de agua mineral con condensación"
    },
    {
      id: 6,
      name: "Refresco de Cola",
      price: 25.00,
      category: "bebidas",
      description: "Refresco de cola 600ml",
      image: "https://placeholder-image-service.onrender.com/image/300x200?prompt=Cold%20cola%20soft%20drink%20in%20a%20glass%20bottle&id=cola-drink",
      alt: "Refresco de cola frío en botella de vidrio"
    },
    {
      id: 7,
      name: "Jugo de Naranja",
      price: 35.00,
      category: "bebidas",
      description: "Jugo de naranja natural 1L",
      image: "https://placeholder-image-service.onrender.com/image/300x200?prompt=Fresh%20orange%20juice%20in%20a%20glass%20pitcher&id=juice-orange",
      alt: "Jugo de naranja fresco en jarra de vidrio"
    },
    {
      id: 8,
      name: "Agua Saborizada",
      price: 22.00,
      category: "bebidas",
      description: "Agua con sabor a frutas 500ml",
      image: "https://placeholder-image-service.onrender.com/image/300x200?prompt=Fruit%20flavored%20water%20bottles%20with%20lemon%20slices&id=water-flavored",
      alt: "Botellas de agua saborizada con rodajas de limón"
    },

    // Utensilios de cocina
    {
      id: 9,
      name: "Set de Cuchillos",
      price: 280.00,
      category: "utensilios",
      description: "Set de 3 cuchillos de acero inoxidable",
      image: "https://placeholder-image-service.onrender.com/image/300x200?prompt=Professional%20knife%20set%20on%20a%20wooden%20block&id=knives-set",
      alt: "Set profesional de cuchillos en bloque de madera"
    },
    {
      id: 10,
      name: "Tabla para Picar",
      price: 120.00,
      category: "utensilios",
      description: "Tabla de madera para picar alimentos",
      image: "https://placeholder-image-service.onrender.com/image/300x200?prompt=Wooden%20cutting%20board%20with%20vegetables&id=board-cutting",
      alt: "Tabla de madera para cortar con verduras"
    },
    {
      id: 11,
      name: "Batidora Manual",
      price: 150.00,
      category: "utensilios",
      description: "Batidora manual de acero inoxidable",
      image: "https://placeholder-image-service.onrender.com/image/300x200?prompt=Stainless%20steel%20hand%20mixer%20on%20counter&id=mixer-hand",
      alt: "Batidora manual de acero inoxidable sobre encimera"
    },
    {
      id: 12,
      name: "Set de Tuppers",
      price: 190.00,
      category: "utensilios",
      description: "Set de 5 tuppers herméticos",
      image: "https://placeholder-image-service.onrender.com/image/300x200?prompt=Set%20of%20plastic%20food%20storage%20containers&id=containers-food",
      alt: "Conjunto de recipientes plásticos para almacenar comida"
    }
  ];

  const addToCart = (product: Product) => {
    setCart(prevCart => {
      const existingItem = prevCart.find(item => item.id === product.id);
      if (existingItem) {
        return prevCart.map(item =>
          item.id === product.id
            ? { ...item, quantity: item.quantity + 1 }
            : item
        );
      }
      return [...prevCart, { ...product, quantity: 1 }];
    });
  };

  const removeFromCart = (productId: number) => {
    setCart(prevCart => prevCart.filter(item => item.id !== productId));
  };

  const updateQuantity = (productId: number, quantity: number) => {
    if (quantity === 0) {
      removeFromCart(productId);
      return;
    }
    setCart(prevCart =>
      prevCart.map(item =>
        item.id === productId ? { ...item, quantity } : item
      )
    );
  };

  const getTotal = () => {
    return cart.reduce((total, item) => total + item.price * item.quantity, 0);
  };

  const getCategoryProducts = (category: string) => {
    return products.filter(product => product.category === category);
  };

  const handleConfirmOrder = () => {
    setOrderConfirmed(true);
    setCurrentView("confirmation");
  };

  const handleSendToWhatsApp = () => {
    const orderDetails = cart.map(item => `${item.quantity}x ${item.name} - $${(item.price * item.quantity).toFixed(2)}`).join('\n');
    const dateStr = selectedDate ? format(selectedDate, "PPP", { locale: es }) : 'No especificada';
    const message = `¡Nuevo pedido de Mercadito Express!\n\nDetalles del pedido:\n${orderDetails}\n\nTotal: $${getTotal().toFixed(2)}\nFecha de recogida: ${dateStr} a las ${selectedTime}\n\n¡Por favor confirma la disponibilidad!`;
    
    const whatsappUrl = `https://wa.me/${whatsappNumber}?text=${encodeURIComponent(message)}`;
    window.open(whatsappUrl, '_blank');
  };

  const availableTimes = [
    "09:00", "10:00", "11:00", "12:00", "13:00", "14:00", "15:00", "16:00", "17:00"
  ];

  const renderProducts = () => (
    <div className="space-y-6">
      <div className="text-center mb-8">
        <h1 className="text-3xl font-bold text-primary">Mercadito Express</h1>
        <p className="text-muted-foreground">Tu tienda de productos diarios</p>
      </div>

      {/* Golosinas */}
      <div>
        <h2 className="text-2xl font-semibold mb-4 text-orange-600">Golosinas</h2>
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
          {getCategoryProducts("golosinas").map(product => (
            <Card key={product.id} className="h-full">
              <div className="h-48 overflow-hidden">
                <img 
                  src={product.image} 
                  alt={product.alt}
                  className="w-full h-full object-cover"
                />
              </div>
              <CardHeader>
                <CardTitle className="text-lg">{product.name}</CardTitle>
                <CardDescription>{product.description}</CardDescription>
              </CardHeader>
              <CardContent>
                <p className="text-2xl font-bold text-primary">${product.price.toFixed(2)}</p>
              </CardContent>
              <CardFooter>
                <Button onClick={() => addToCart(product)} className="w-full bg-orange-600 hover:bg-orange-700">
                  Añadir al carrito
                </Button>
              </CardFooter>
            </Card>
          ))}
        </div>
      </div>

      {/* Bebidas */}
      <div>
        <h2 className="text-2xl font-semibold mb-4 text-blue-600">Aguas y Bebidas</h2>
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
          {getCategoryProducts("bebidas").map(product => (
            <Card key={product.id} className="h-full">
              <div className="h-48 overflow-hidden">
                <img 
                  src={product.image} 
                  alt={product.alt}
                  className="w-full h-full object-cover"
                />
              </div>
              <CardHeader>
                <CardTitle className="text-lg">{product.name}</CardTitle>
                <CardDescription>{product.description}</CardDescription>
              </CardHeader>
              <CardContent>
                <p className="text-2xl font-bold text-primary">${product.price.toFixed(2)}</p>
              </CardContent>
              <CardFooter>
                <Button onClick={() => addToCart(product)} className="w-full bg-blue-600 hover:bg-blue-700">
                  Añadir al carrito
                </Button>
              </CardFooter>
            </Card>
          ))}
        </div>
      </div>

      {/* Utensilios */}
      <div>
        <h2 className="text-2xl font-semibold mb-4 text-green-600">Utensilios de Cocina</h2>
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
          {getCategoryProducts("utensilios").map(product => (
            <Card key={product.id} className="h-full">
              <div className="h-48 overflow-hidden">
                <img 
                  src={product.image} 
                  alt={product.alt}
                  className="w-full h-full object-cover"
                />
              </div>
              <CardHeader>
                <CardTitle className="text-lg">{product.name}</CardTitle>
                <CardDescription>{product.description}</CardDescription>
              </CardHeader>
              <CardContent>
                <p className="text-2xl font-bold text-primary">${product.price.toFixed(2)}</p>
              </CardContent>
              <CardFooter>
                <Button onClick={() => addToCart(product)} className="w-full bg-green-600 hover:bg-green-700">
                  Añadir al carrito
                </Button>
              </CardFooter>
            </Card>
          ))}
        </div>
      </div>

      {/* Carrito flotante */}
      {cart.length > 0 && (
        <div className="fixed bottom-4 right-4">
          <Button 
            onClick={() => setCurrentView("cart")}
            className="bg-primary text-white px-6 py-3 rounded-lg shadow-lg"
          >
            Ver Carrito ({cart.reduce((total, item) => total + item.quantity, 0)})
          </Button>
        </div>
      )}
    </div>
  );

  const renderCart = () => (
    <div className="max-w-4xl mx-auto">
      <h2 className="text-2xl font-bold mb-6 text-center">Tu Carrito de Compras</h2>
      
      {cart.length === 0 ? (
        <div className="text-center py-12">
          <p className="text-muted-foreground">Tu carrito está vacío</p>
          <Button onClick={() => setCurrentView("products")} className="mt-4">
            Volver a productos
          </Button>
        </div>
      ) : (
        <div className="space-y-4">
          {cart.map(item => (
            <Card key={item.id}>
              <CardContent className="p-4 flex justify-between items-center">
                <div>
                  <h3 className="font-semibold">{item.name}</h3>
                  <p className="text-muted-foreground">${item.price.toFixed(2)} c/u</p>
                </div>
                <div className="flex items-center gap-4">
                  <div className="flex items-center gap-2">
                    <Button
                      variant="outline"
                      size="sm"
                      onClick={() => updateQuantity(item.id, item.quantity - 1)}
                    >
                      -
                    </Button>
                    <span className="w-8 text-center">{item.quantity}</span>
                    <Button
                      variant="outline"
                      size="sm"
                      onClick={() => updateQuantity(item.id, item.quantity + 1)}
                    >
                      +
                    </Button>
                  </div>
                  <p className="font-bold">${(item.price * item.quantity).toFixed(2)}</p>
                  <Button
                    variant="destructive"
                    size="sm"
                    onClick={() => removeFromCart(item.id)}
                  >
                    Eliminar
                  </Button>
                </div>
              </CardContent>
            </Card>
          ))}
          
          <Card className="bg-muted">
            <CardContent className="p-6">
              <div className="flex justify-between items-center text-lg">
                <span className="font-semibold">Total:</span>
                <span className="font-bold text-2xl text-primary">${getTotal().toFixed(2)}</span>
              </div>
            </CardContent>
          </Card>

          <div className="flex gap-4 justify-center">
            <Button variant="outline" onClick={() => setCurrentView("products")}>
              Seguir comprando
            </Button>
            <Button onClick={() => setCurrentView("schedule")}>
              Programar recogida
            </Button>
          </div>
        </div>
      )}
    </div>
  );

  const renderSchedule = () => (
    <div className="max-w-2xl mx-auto">
      <h2 className="text-2xl font-bold mb-6 text-center">Programar Recogida</h2>
      
      <div className="space-y-6">
        <Card>
          <CardHeader>
            <CardTitle>Resumen del Pedido</CardTitle>
          </CardHeader>
          <CardContent>
            {cart.map(item => (
              <div key={item.id} className="flex justify-between py-2 border-b">
                <span>{item.quantity}x {item.name}</span>
                <span>${(item.price * item.quantity).toFixed(2)}</span>
              </div>
            ))}
            <div className="flex justify-between font-bold text-lg pt-4">
              <span>Total:</span>
              <span>${getTotal().toFixed(2)}</span>
            </div>
          </CardContent>
        </Card>

        <Card>
          <CardHeader>
            <CardTitle>Selecciona Fecha y Hora</CardTitle>
          </CardHeader>
          <CardContent className="space-y-4">
            <div>
              <Label className="mb-2 block">Fecha de recogida</Label>
              <Popover>
                <PopoverTrigger asChild>
                  <Button
                    variant="outline"
                    className="w-full justify-start text-left font-normal"
                  >
                    <CalendarIcon className="
