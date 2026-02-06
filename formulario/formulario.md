## 📘 CookBook - Formulários no Fluig

## ✨ Introdução

Fala, dev! 👨‍💻👩‍💻

Aqui você vai encontrar de tudo um pouco sobre **formulários no Fluig**, desde os truques mais básicos até umas paradas mais cabulosas. A ideia é facilitar sua vida na construção dos formulários, te dando atalhos, boas práticas e alguns “macetes” que só quem apanha no dia a dia conhece 😂

### O que você vai ver por aqui:

*   Tipos de campos
*   Tabela pai x filho
*   Estilização de todos os tipos
*   Componentes padrões e customizados
*   Validação de dados (beforeSendValidate)

---

## 🧾 Campos

### 📥 Input texto

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

#### 1\. Importe a lib no `<head>` do seu formulário:

```html
<script src="https://unpkg.com/imask"></script>
```

#### 2\. HTML do input (tem que ser `type="text"`):

```html
<input id="VALORTOTAL" name="VALORTOTAL" class="form-control" type="text" placeholder="R$ 0,00">
```

#### 3\. Máscara com JS (coloque na inicialização do formulário):

```plaintext
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

```plaintext
const valorBruto = document.getElementById('VALORTOTAL').value;
const valorLimpo = valorBruto.replace('R$', '').replace(/\./g, '').replace(',', '.');
const valorFloat = parseFloat(valorLimpo);
```

Assim você evita bugs e ainda mantém o padrão certo pro backend

---

### 📥 Input Zoom

Campos do tipo zoom servem para carregarmos uma lista de opções ao usuário que possibilita buscarmos um cadastro no ERP ou então de cadastros internos do Fluig, como no exemplo abaixo o dataset “colleague” que retorna os usuários cadastrados na plataforma.

Todas as configurações do campo deverão ser atribuídas ao objeto da propriedade data-zoom (nome obrigatório para a propriedade);

1.  `displayKey`: Coluna do dataset que ficará visível no formulário, após selecionar e que também será o valor atribuído no formulário.
2.  `maximumSelectionLength`: Quantidade máxima de registros que poderá ser selecionado no campo.
3.  `datasetId`: Nome do dataset que deverá ser listado no campo.
4.  `placeholder`: Instrução para que o usuário, antes de selecionar o campo, tenha maiores detalhes do que deverá ser preenchido ali.
5.  `fields`: Todas as colunas que serão visíveis (ou não hehe') no formulário quando o campo for aberto.
    1.  `field`: Nome da coluna no dataset que será visível;
    2.  `label`: Nome que será atribuído no formulário para facilitar o entendimento do usuário;
    3.  `visible`: Se essa coluna será visível ou não, nesse caso, podendo ser apenas para preencher campos futuros com a função setSelectedZoomItem (ainda chegaremos lá);
    4.  `standard`: Valor default é false, caso coloque como true, o Fluig utilizará essa coluna para ordernar os registros de forma crescente;
    5.  `search`: ~~Se essa coluna deverá ser a padrão utilizada como constraint quando o usuário começar a digitar o valor e enviar ao dataset para filtrar os resultados;~~ conforme reportardo pelo Bruno Gasparetto [Issue #1](https://github.com/leofioretti-dev/cookbook-fluig/issues/1) essa funcionalidade busca, na verdade, da coluna que está no displayKey e não da coluna que está atribuído. Será aberto um chamado na TOTVS para identificar se é uma falha da aplicação ou da documentação;

```html
<input type="zoom" class="form-control" id="USUARIO" name="USUARIO"
	data-zoom="{
		'displayKey':'colleagueName',
		'maximumSelectionLength':'1',
		'datasetId':'colleague',
		'placeholder':'Escolha um usuário',
		'fields':[
			{
				'field':'colleagueName',
				'label':'Banco PIS',
				'visible':'true',
				'standard':'true',
				'search':'true',
			}
		]
	}"
/>
```

---

### 📥 Zoom com preenchimento automático de campo oculto

Ao invés de ter que ficar criando lógicas no setSelectedZoomItem para preencher o campo oculto com o código, podemos fazer isso diretamente na declaração do campo, utilizando a propriedade `zoomvalue`.

Nessa propriedade será atribuída a coluna que deverá ser levada como valor para o campo oculto.

**Atenção:** deverá criar um campo input do tipo hidden com a seguinte estrutura de id no campo: `hidden_MESMOIDCAMPOZOOM` para que o Fluig associe os dois campos e preencha automaticamente.

```html
<div class="form-group">
    <label for="zoomfield0">Usuário</label>
    <input type="zoom" class="form-control" id="user" name="user" zoomvalue="colleagueId" data-zoom="
        {
            'displayKey':'colleagueName',
            'datasetId':'colleague',
            'fields':[{
                'field':'colleagueId',
                'label':'colleagueId',
                'visible': 'false'
            },{
                'field':'colleagueName',
                'label':'colleagueName',
                'standard':true
            },{
                'field':'mail',
                'label':'mail'
            }]
        }">
    <input type="hidden" class="form-control" id="hidden_user" name="hidden_user"> <!-- este campo irá armazenar o campo 'collegueId' -->
</div>
```

---

Curtiu? Já salva esse pedaço aí no seu repositório e segue explorando o restante do nosso CookBook! 🚀
