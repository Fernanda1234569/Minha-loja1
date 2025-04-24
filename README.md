<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Loja Online</title>
  <style>
    /* Reset de estilo */
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    /* Cor de fundo e tipografia */
    body {
      font-family: Arial, sans-serif;
      background-color: #f4f4f4;
      color: #333;
    }

    /* Estilos de header */
    header {
      background-color: #333;
      padding: 10px 0;
    }

    nav ul {
      list-style: none;
      display: flex;
      justify-content: center;
    }

    nav ul li {
      margin: 0 20px;
    }

    nav ul li a {
      color: #fff;
      text-decoration: none;
      font-size: 16px;
      transition: color 0.3s ease;
    }

    nav ul li a:hover {
      color: #e3e3e3;
    }

    /* Estilos para seção de produtos */
    .produtos {
      display: flex;
      justify-content: space-around;
      margin: 30px;
      flex-wrap: wrap;
    }

    .produto {
      text-align: center;
      padding: 20px;
      background-color: white;
      border-radius: 8px;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
      width: 200px;
      transition: transform 0.3s ease;
      margin: 10px;
    }

    .produto img {
      width: 100%;
      height: auto;
      border-radius: 8px;
    }

    .produto:hover {
      transform: scale(1.05);
    }

    /* Carrinho */
    .shopping-cart {
      margin: 20px;
      background-color: white;
      padding: 20px;
      border-radius: 8px;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    }

    .cart-item {
      display: flex;
      justify-content: space-between;
      padding: 10px;
    }

    .cart-summary {
      margin-top: 20px;
      text-align: center;
    }

    .checkout-button {
      padding: 10px 20px;
      background-color: #28a745;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }

    .checkout-button:hover {
      background-color: #218838;
    }

    /* Estilos para o formulário de login e cadastro */
    form input, form button {
      width: 100%;
      padding: 10px;
      margin: 10px 0;
      border-radius: 5px;
      border: 1px solid #ccc;
    }

    form button {
      background-color: #007bff;
      color: white;
      cursor: pointer;
    }

    form button:hover {
      background-color: #0056b3;
    }

    /* Áreas privadas para desenvolvedor */
    .developer-login, .developer-area {
      display: none;
      margin: 20px;
    }

    .developer-login input, .developer-login button {
      width: 100%;
    }

    .developer-area button {
      margin-top: 10px;
      padding: 10px 20px;
      background-color: #dc3545;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }

    .developer-area button:hover {
      background-color: #c82333;
    }

    #developer-error {
      color: red;
      display: none;
    }

    /* Responsividade */
    @media screen and (max-width: 768px) {
      .produtos {
        flex-direction: column;
        align-items: center;
      }

      .produto {
        width: 80%;
        margin-bottom: 20px;
      }
    }
  </style>
</head>
<body>
  <!-- Header -->
  <header>
    <nav>
      <ul>
        <li><a href="#">Home</a></li>
        <li><a href="#">Produtos</a></li>
        <li><a href="#">Carrinho</a></li>
        <li><a href="#">Login</a></li>
        <li><a href="#">Desenvolvedor</a></li>
      </ul>
    </nav>
  </header>

  <!-- Seção de Produtos -->
  <section class="produtos" id="product-list">
    <!-- Produtos serão carregados via JavaScript -->
  </section>

  <!-- Carrinho de Compras -->
  <section class="shopping-cart">
    <h2>Carrinho de Compras</h2>
    <div id="cart-items"></div>
    <div class="cart-summary">
      <p id="cart-total">Total: R$ 0,00</p>
      <button id="checkout-btn" onclick="checkout()">Finalizar Compra</button>
    </div>
  </section>

  <!-- Login do Desenvolvedor -->
  <section id="developer-login" class="developer-login">
    <h3>Área do Desenvolvedor</h3>
    <form id="developer-login-form">
      <input type="password" id="developer-password" placeholder="Digite a senha do desenvolvedor" required>
      <button type="submit">Entrar</button>
      <p id="developer-error">Senha incorreta! Tente novamente.</p>
    </form>
  </section>

  <!-- Área do Desenvolvedor -->
  <section id="developer-area" class="developer-area">
    <h3>Bem-vindo, Desenvolvedor</h3>
    <button onclick="modifyProduct()">Modificar Produto</button>
    <button onclick="modifyPrice()">Modificar Preço</button>
    <button onclick="changeLogo()">Alterar Logo</button>
    <button onclick="logoutDeveloper()">Sair</button>
  </section>

  <script>
    const cart = [];
    let isDeveloperAuthenticated = false;
    const products = [
      { id: 1, name: "Produto 1", price: 100, img: "produto1.jpg" },
      { id: 2, name: "Produto 2", price: 200, img: "produto2.jpg" },
      { id: 3, name: "Produto 3", price: 150, img: "produto3.jpg" },
      { id: 4, name: "Produto 4", price: 250, img: "produto4.jpg" },
    ];

    // Carregar lista de produtos
    function loadProducts() {
      const productListElement = document.getElementById('product-list');
      products.forEach(product => {
        const productElement = document.createElement('div');
        productElement.classList.add('produto');
        productElement.innerHTML = `
          <img src="${product.img}" alt="${product.name}">
          <h3>${product.name}</h3>
          <p>R$ ${product.price.toFixed(2)}</p>
          <button onclick="addToCart('${product.name}', ${product.price})">Adicionar ao Carrinho</button>
        `;
        productListElement.appendChild(productElement);
      });
    }

    loadProducts();

    // Função para adicionar produto ao carrinho
    function addToCart(name, price) {
      const existingProduct = cart.find(item => item.name === name);
      if (existingProduct) {
        existingProduct.quantity += 1;
      } else {
        cart.push({ name, price, quantity: 1 });
      }
      updateCart();
    }

    // Função para atualizar o carrinho na tela
    function updateCart() {
      const cartItemsElement = document.getElementById('cart-items');
      const cartTotalElement = document.getElementById('cart-total');
      const checkoutBtn = document.getElementById('checkout-btn');
      
      cartItemsElement.innerHTML = '';

      let total = 0;
      cart.forEach(item => {
        const itemElement = document.createElement('div');
        itemElement.classList.add('cart-item');
        itemElement.innerHTML = `
          <p>${item.name} x ${item.quantity}</p>
          <p>R$ ${item.price.toFixed(2)}</p>
          <button onclick="removeFromCart('${item.name}')">Remover</button>
        `;
        cartItemsElement.appendChild(itemElement);
        total += item.price * item.quantity;
      });

      cartTotalElement.textContent = `R$ ${total.toFixed(2)}`;

      checkoutBtn.style.display = cart.length > 0 ? 'block' : 'none';
    }

    // Função para remover item do carrinho
    function removeFromCart(name) {
      const index = cart.findIndex(item => item.name === name);
      if (index !== -1) {
        cart.splice(index, 1);
        updateCart();
      }
    }

    // Função para finalizar a compra
    function checkout() {
      const token = localStorage.getItem('token');
      if (!token) {
        alert('Você precisa estar logado para realizar a compra!');
        return;
      }
      
      alert('Compra finalizada com sucesso!');
    }

    // Função para login de desenvolvedor
    document.getElementById('developer-login-form').addEventListener('submit', (e) => {
      e.preventDefault();
      const password = document.getElementById('developer-password').value;
      
      if (password === "SuaSenhaDeDesenvolvedor") {
        isDeveloperAuthenticated = true;
        document.getElementById('developer-login').style.display = 'none';
        document.getElementById('developer-area').style.display = 'block';
        document.getElementById('developer-error').style.display = 'none';
      } else {
        document.getElementById('developer-error').style.display = 'block';
      }
    });

    // Função para modificar um produto (exemplo)
    function modifyProduct() {
      alert("Modificar Produto");
    }

    // Função para modificar o preço de um produto
    function modifyPrice() {
      alert("Modificar preço de produto");
    }

    // Função para alterar o logo
    function changeLogo() {
      alert("Alterar logo");
    }

    // Função de logout da área do desenvolvedor
    function logoutDeveloper() {
      isDeveloperAuthenticated = false;
      document.getElementById('developer-area').style.display = 'none';
      document.getElementById('developer-login').style.display = 'block';
    }
  </script>
</body>
</html>
