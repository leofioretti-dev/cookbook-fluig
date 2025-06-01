
# 📘 CookBook - Formulários no Fluig

## ✨ Introdução

Fala, dev! 👨‍💻👩‍💻

Aqui você vai encontrar de tudo um pouco sobre **formulários no Fluig**, desde os truques mais básicos até umas paradas mais cabulosas. A ideia é facilitar sua vida na construção dos formulários, te dando atalhos, boas práticas e alguns “macetes” que só quem apanha no dia a dia conhece 😂

### O que você vai ver por aqui:

- Tipos de campos
- Tabela pai x filho
- Estilização de todos os tipos
- Componentes padrões e customizados
- Validação de dados (beforeSendValidate)

---

## 🧾 Campos

### 📥 Input

📌 Estrutura base:

```html
<input id="IDCAMPO" name="NAMECAMPO" class="form-control" type="text"/>
```

O Fluig usa o **Bootstrap** como base de estilização. Ou seja, a classe `form-control` já vem com um visual arrumadinho. Depois, é só customizar com seu próprio CSS, se quiser dar aquele tapa no layout 🎨

💡 **Dica esperta**: Sempre use o mesmo valor para o `id` e o `name`. Isso evita dor de cabeça quando você precisar manipular o campo via JavaScript, workflow, dataset... enfim, em tudo!

---

### 🔢 Input tipo number

```html
<input id="IDCAMPO" name="NAMECAMPO" class="form-control" type="number"/>
```

Esse carinha restringe a entrada para números apenas, mas... cuidado com o `e`, `+` e `-`, que ainda podem ser digitados por conta do suporte a notação científica. Se quiser travar geral, use um scriptzinho pra bloquear essas letras.

---

### 🚫 Tirar as setinhas de +/−

Se você quer um input mais “limpo”, sem aquelas setinhas do tipo number, dá pra remover com uma classe CSS:

```html
<input id="IDCAMPO" name="NAMECAMPO" class="form-control sem-setas" type="number"/>

<style>
.sem-setas::-webkit-inner-spin-button { 
    -webkit-appearance: none !important;
}
.sem-setas { 
   -moz-appearance: textfield !important;
   appearance: textfield !important;
}
</style>
```

---

### 💸 Input com máscara de moeda (R$)

Infelizmente o HTML puro não tem um tipo "moeda", mas a gente dá o jeitinho com um JS maroto:

#### 1. Importe a lib no `<head>` do seu formulário:

```html
<script src="https://unpkg.com/imask"></script>
```

#### 2. HTML do input (tem que ser `type="text"`):

```html
<input id="VALORTOTAL" name="VALORTOTAL" class="form-control" type="text" placeholder="R$ 0,00">
```

#### 3. Máscara com JS (coloque na inicialização do formulário):

```js
var element = document.getElementById('VALORTOTAL');
var maskOptions = {
    mask: 'R$ num',
    blocks: {
        num: {
            mask: Number,
            thousandsSeparator: '.',
            radix: ',',
            mapToRadix: ['.'],
            scale: 2,
            signed: false,
            normalizeZeros: true,
            padFractionalZeros: true,
        }
    }
};
IMask(element, maskOptions);
```

---

### ✅ Dica extra: Enviar para o backend sem bagunça

Antes de salvar no banco ou enviar via dataset, não esquece de limpar a máscara:

```js
const valorBruto = document.getElementById('VALORTOTAL').value;
const valorLimpo = valorBruto.replace('R$', '').replace(/\./g, '').replace(',', '.');
const valorFloat = parseFloat(valorLimpo);
```

Assim você evita bugs e ainda mantém o padrão certo pro backend

---

Curtiu? Já salva esse pedaço aí no seu repositório e segue explorando o restante do nosso CookBook! 🚀
