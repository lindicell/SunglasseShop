<template>
<body>
  <nav
    class="navbar navbar-expand-lg navbar-dark ftco_navbar bg-dark ftco-navbar-light"
    id="ftco-navbar"
  >
    <div class="container">
      <p>
        <img src="./assets/images/logo.png" align="left" width="30" height="30">
        <a class="navbar-brand">SunGlasses Shop</a>
      </p>
    </div>
  </nav>
  <div>
    <hr>
    <div class="dropdown">
      <button class="dropbtn">Meu carrinho : {{this.selectedProducts.length}} produtos</button>
      <div class="dropdown-content">
        <div v-if="cartTotal" class="dropdown-title">
          <p>Produto</p>
          <p>Quantidade</p>
          <p>Preço Unitário</p>
          <p>Remover</p>
        </div>
        <div v-if="cartTotal" class="dropdown-title" v-for="item in cart" :key="item.id">
          <p>{{item.product}}</p>
          <p>{{item.quantity}}</p>
          <p>{{item.price}}</p>
          <button @click="removeItem(item)">X</button>
        </div>
        <div v-if="cartTotal">Total R${{cartTotal.toFixed(2)}}</div>
        <div v-else>Carrinho Vazio</div>
      </div>
    </div>
    <div class="container">
      <div v-for="item in products" :key="item.id">
        <h2>{{ item.name }}</h2>
        <img class="img" :src="item.image" :alt="item.name">
        <p>{{item.description}}</p>
        <button @click="showDetails(item)">Detalhes do Produto</button>
        <h4>R${{ item.price }}</h4>
        <hr>
        <label for="qty">Selecione a Quantidade:</label>
        <input type="number" id="qty" name="qty" size="4" @change="addQuantity($event,item)">
        <br>
        <button @click="addCart(item)">Comprar</button>
      </div>
    </div>
    <modals-container></modals-container>
  </div>
</body>
</template>

<script>
export default {
  data() {
    return {
      products: [
        {
          id: 0,
          name: "Óculos Jazz",
          description: "Óculos De Sol Feminino Jazz Preto",
          longDescription:
            "O óculos de sol é um acessório indispensável para os dias ensolarados.É produzido com material em termoplástico semicristalino usado em engenharia espacial e automotiva para garantir leveza fora do comum.",
          price: 29.9,
          stock: 5,
          image: require("./assets/images/produto1.jpeg"),
          quantity: 0
        },
        {
          id: 1,
          name: "Óculos Rock",
          description: "Óculos De Sol Feminino Rock",
          longDescription:
            "O óculos de sol é um acessório indispensável para os dias ensolarados.É produzido com material em termoplástico semicristalino usado em engenharia espacial e automotiva para garantir leveza fora do comum.",
          price: 19.9,
          stock: 2,
          image: require("./assets/images/produto2.jpg"),
          quantity: 0
        },
        {
          id: 2,
          name: "Óculos Samba",
          description: "Óculos De Sol Feminino Samba",
          longDescription:
            "O óculos de sol é um acessório indispensável para os dias ensolarados.É produzido com material em termoplástico semicristalino usado em engenharia espacial e automotiva para garantir leveza fora do comum.",
          price: 9.9,
          stock: 1,
          image: require("./assets/images/produto3.jpg"),
          quantity: 0
        }
      ],
      cart: [],
      selectedProducts: [],
      cartTotal: 0
    };
  },

  methods: {
    showDetails(item) {
      this.$modal.show(
        {
          template: `
      <div class="modal" style="font-style:italic,background-color:#d7d7d7,text-align:center;">
      <p>>DETALHES DO PRODUTO<</p>
      <hr>
      <h4>{{name}}</h4>
      <h5>{{longDescription}}</h5>
      <h4>Preço: R$ {{price}}</h4>
      <h4>Quantidade em estoque: {{stock}}</h4>
      <table>
      <tr>
        <th>Modelo</th>
        <th>Cor</th>
        <th>Tipo de Lente</th>
    </tr>
    <tr>
    <td>OO9102</td>
    <td>Preto Fosco/Prata</td>
    <td>Comum</td>
    </tr>
  </table>
      </div>
          `,
          props: ["name", "longDescription", "price", "stock"]
        },
        {
          name: item.name,
          longDescription: item.longDescription,
          price: item.price,
          stock: item.stock
        },
        {
          height: "400px"
        }
      );
    },
    calculateTotal() {
      this.cartTotal = this.cart.reduce(
        (accumulator, currentValue) => currentValue.total + accumulator,
        0
      );
    },
    addCart(item) {
      let selectedItem = {};
      let indexOfProducts = this.selectedProducts.indexOf(item.name);
      if (indexOfProducts !== -1) {
        this.cart[indexOfProducts].quantity += item.quantity;
        this.cart[indexOfProducts].total =
          (this.cart[indexOfProducts].quantity || 0) * item.price;
        this.products[item.id].stock -= item.quantity;
      } else {
        this.selectedProducts.push(item.name);
        selectedItem = {
          id: item.id,
          product: item.name,
          price: item.price,
          quantity: item.quantity,
          total: item.quantity * item.price
        };
        this.cart.push(selectedItem);
        this.products[item.id].stock -= item.quantity;
      }
      this.calculateTotal();
    },
    removeItem(item) {
      let indexOfProducts = this.selectedProducts.indexOf(item.product);
      if (indexOfProducts !== -1) {
        this.cart.splice(indexOfProducts, 1);
        this.products[indexOfProducts].stock += item.quantity;
        this.selectedProducts.splice(indexOfProducts, 1);
        this.calculateTotal();
        alert("Produto excluído do carrinho");
      } else {
        alert("Produto não existe no carrinho");
      }
    },

    addQuantity(event, item) {
      const value = parseInt(event.target.value, 10);
      if (value <= item.stock) {
        return (this.products[item.id].quantity = value);
      }
      return alert("Quantidade maior que estoque");
    }
  }
};
</script>

<style lang="scss">
@import "./style/style.scss";
</style>