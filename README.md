Manejando eventos
Manejar eventos en elementos de React es muy similar a manejar eventos con elementos del DOM. Hay algunas diferencias de sintaxis:

Los eventos de React se nombran usando camelCase, en vez de minúsculas.
Con JSX pasas una función como el manejador del evento, en vez de un string.
Por ejemplo, el HTML:

<button onclick="activateLasers()">
  Activate Lasers
</button>
En React es algo diferente:

<button onClick={activateLasers}>
  Activate Lasers
</button>
Otra diferencia es que en React no puedes retornar false para prevenir el comportamiento por defecto. Debes, explícitamente, llamar preventDefault. Por ejemplo, en un HTML plano, para prevenir el comportamiento por defecto de un enlace de abrir una nueva página, puedes escribir:

<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>
En cambio en React, esto podría ser:

function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('The link was clicked.');
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}

Aquí, e es un evento sintético. React define estos eventos sintéticos acorde a las especificaciones W3C, para que no tengas que preocuparte por la compatibilidad entre distintos navegadores. Los eventos de React no funcionan exactamente igual que los eventos nativos. Mira la guía de referencia SyntheticEvent para aprender más.

Cuando estás utilizando React, generalmente no necesitas llamar addEventListener para agregar escuchadores de eventos a un elemento del DOM después de que este es creado. En cambio, solo debes proveer un manejador de eventos cuando el elemento se renderiza inicialmente.

Cuando defines un componente usando una clase de ES6, un patrón muy común es que los manejadores de eventos sean un método de la clase. Por ejemplo, este componente Toggle renderiza un botón que permite al usuario cambiar el estado entre “ENCENDIDO” y “APAGADO”:

class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // Este enlace es necesario para hacer que `this` funcione en el callback
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(state => ({
      isToggleOn: !state.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
);

Tienes que tener mucho cuidado en cuanto al significado de this en los callbacks de JSX. En JavaScript, los métodos de clase no están ligados por defecto. Si olvidas ligar this.handleClick y lo pasas a onClick, this será undefined cuando se llame la función.

Esto no es un comportamiento especifico de React; esto hace parte de como operan las funciones JavaScript. Generalmente, si refieres un método sin usar () después de este, tal como onClick={this.handleClick}, deberías ligar ese método.

Pasando argumentos a escuchadores de eventos
Dentro de un bucle es muy común querer pasar un parámetro extra a un manejador de eventos. Por ejemplo, si id es el ID de una fila, cualquiera de los códigos a continuación podría funcionar:

<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
Las dos líneas anteriores son equivalentes, y utilizan funciones flecha y Function.prototype.bind respectivamente.

En ambos casos, el argumento e que representa el evento de React va a ser pasado como un segundo argumento después del ID. Con una función flecha, tenemos que pasarlo explícitamente, pero con bind cualquier argumento adicional es pasado automáticamente