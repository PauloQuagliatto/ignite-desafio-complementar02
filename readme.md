# Passo a passo do desafio

## Mudar as classes para componentes

#### Onde houver a palavar _class_ você substituirá o código desta forma:

> ##### Este código:

```js
class Food extends Component {
    ...
}
```

> ##### Virará este código:

```js
const Food = () => {
    ...
}
```

---

## Localizar as passagens de propriedades e funcionamentos de estados e substituir por _HOOKS_

#### Procure por _constructors_ dentro das classes

```js
const Food = () => {
  constructor(props) {
    super(props);

    const { available } = this.props.food;
    this.state = {
      isAvailable: available
    };
  }
  ...
}
```

#### Agora substitua por _HOOKS_

```jsx
const Food = (props) => {
    const { available } = props.food;
    const [isAvailable, setIsAvailable] = useState(available);
    ...
}
```

> Não esqueça de retirar a palavra <span style="color:#c70000">_this_</span> e adicionar um <span style="color:#00FF">_const_</span> ou <span style="color:#00FF">_let_</span> na frente.

---

## Agora retire os métodos _render()_ dos componentes

```js
const Food = (props) => {
    const { available } = props.food;
    const [isAvailable, setIsAvailable] = useState(available);
    ...
    render () {
        return (
            ...
        )
    }
}
```

```js
const Food = (props) => {
    const { available } = props.food;
    const [isAvailable, setIsAvailable] = useState(available);
    ...
    return (
        ...
    )
}
```

> Não esqueça de tirar a <span style="color:#c50000">}</span> que fica ao final para não dar conflito

---

## Agora hora de adicionar o ts para começar a mexer com typescript

#### Rode o comando abaixo no node para baixar as dependências do typescript necessárias

```js
npm i -D typescript @types/jest @types/node @types/react @types/react-dom @types/react-modal @types/react-router-dom @types/styled-components @types/yup
```

#### Com as dependências instaladas, crie um arquivo tsconfig.json e cole as configurações abaixo:

```json
{
  "compilerOptions": {
    "target": "es5",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "noFallthroughCasesInSwitch": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx"
  },
  "include": ["src", "custom.d.ts"]
}
```

> Não esqueça o arquivo **_react-app-d.ts_** como pedido no desafio, olhe no site.

> DICA: Criei um arquivo **_custom.d.ts_** para as importações de arquivos svg, veja no "include" do arquivo json que ele importa este código:

```js
declare module "*.svg" {
    const content: any;
    export default content;
  }
```

#### Agora é adicionar as interfaces e types aos arquivos e mudar as extensões de **_js_** e **_jsx_** para **_ts_** e **_tsx_**

##### Eis meu Food.tsx final:

```tsx
import { useState } from "react";
import { FiEdit3, FiTrash } from "react-icons/fi";

import { Container } from "./styles";
import api from "../../services/api";

import { IFood } from "../../../types";

interface IProps {
  food: IFood;
  handleDelete: (id: number) => void;
  handleEditFood: (food: IFood) => void;
}

const Food = ({ food, handleDelete, handleEditFood }: IProps) => {
  const { available } = food;
  const [isAvailable, setIsAvailable] = useState(available);

  const toggleAvailable = async () => {
    await api.put(`/foods/${food.id}`, {
      ...food,
      available: !isAvailable,
    });

    setIsAvailable(!isAvailable);
  };

  const setEditingFood = () => {
    handleEditFood(food);
  };

  return (
    <Container available={isAvailable}>
      <header>
        <img src={food.image} alt={food.name} />
      </header>
      <section className="body">
        <h2>{food.name}</h2>
        <p>{food.description}</p>
        <p className="price">
          R$ <b>{food.price}</b>
        </p>
      </section>
      <section className="footer">
        <div className="icon-container">
          <button
            type="button"
            className="icon"
            onClick={setEditingFood}
            data-testid={`edit-food-${food.id}`}
          >
            <FiEdit3 size={20} />
          </button>

          <button
            type="button"
            className="icon"
            onClick={() => handleDelete(food.id)}
            data-testid={`remove-food-${food.id}`}
          >
            <FiTrash size={20} />
          </button>
        </div>

        <div className="availability-container">
          <p>{isAvailable ? "Disponível" : "Indisponível"}</p>

          <label htmlFor={`available-switch-${food.id}`} className="switch">
            <input
              id={`available-switch-${food.id}`}
              type="checkbox"
              checked={isAvailable}
              onChange={toggleAvailable}
              data-testid={`change-status-food-${food.id}`}
            />
            <span className="slider" />
          </label>
        </div>
      </section>
    </Container>
  );
};

export default Food;
```
---

# Divirta-se codando e procurando as respostas para futuros problemas, esse é o caminho para o crescimento!