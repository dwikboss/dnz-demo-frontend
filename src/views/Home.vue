<template>
  <div class="page home">
    <div class="home-blocks">
      <div class="form-block">
        <div class="form-container">
          <h1>Contact</h1>
          <form @submit.prevent="handleSubmit">
            <div class="form-name">
              <input v-model="firstName" type="text" id="firstname" name="firstname" placeholder="Voornaam">
              <input v-model="lastName" type="text" id="lastname" name="lastname" placeholder="Achternaam">
            </div>
            <input v-model="email" type="text" id="email" name="email" placeholder="E-mailadres">
            <textarea v-model="message" id="message" name="contactform" rows="8" cols="50"
              placeholder="Uw bericht"></textarea>
            <input type="submit" value="Verzenden" id="form-btn" />
            <p v-if="emailSent" class="email-notif">E-mail verstuurd!</p>
          </form>
        </div>
      </div>
      <div class="img-block">
        <div class="inventory-items">
          <div v-for="item in inventory" :key="item.SKU" class="item">
            <div class="item-content">
              <div class="product-details">
                <p>{{ item.Onderdeeltitel }}</p>
                <p class="description">{{ item.Onderdeel_beschrijving }}</p>
              </div>
              <p class="price">€{{ item.Prijs_per_stuk }}</p>
            </div>
          </div>
        </div>
        <div class="order-tab" :class="{ 'open': isRequestFinished }">
          <h1>Bestelling</h1>
          <p>Bedankt voor uw bestelling bij De Nieuwe Zaak!</p>
          <div class="order-wrapper" v-if="orderDetails" >
            <div class="customer-details">
              <p class="customer_firstname">{{ orderDetails.first_name }} {{ orderDetails.last_name }}
              </p>
              <p class="customer_lastname">{{ orderDetails.email }}</p>
            </div>
            <div v-for="product in orderDetails.products" :key="product.SKU" class="invoice-product">
              <div class="prod-title">{{ product.Onderdeeltitel }}</div>
              <div class="prod-desc">{{ product.Onderdeel_beschrijving }}</div>
              <div class="product-prices">
                <div class="prod-amount">{{ product.Stuks }}</div>
                <div class="prod-total">{{ product.Totaalprijs }}</div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import axios from 'axios';
import inventory from '@/assets/data/inventory.json';

export default defineComponent({
  name: 'HomeView',
  data() {
    return {
      firstName: '',
      lastName: '',
      email: '',
      message: '',
      orderDetails: null as any,
      inventory: [] as any[],
      isRequestFinished: false,
      emailSent: false,
    };
  },
  async mounted() {
    this.inventory = inventory;
  },
  methods: {
    async handleSubmit() {
      this.emailSent = true;
      try {
        const response = await axios.post('https://www.dnz-demo-backend.vercel.app/contact', {
          firstName: this.firstName,
          lastName: this.lastName,
          email: this.email,
          message: this.message,
          inventory: inventory
        });

        this.orderDetails = JSON.parse(response.data);
        this.isRequestFinished = true;
        console.log(this.orderDetails);
      } catch (error) {
        console.error('Error sending contact form:', error);
      }
    }
  }
});
</script>

<style lang="scss" scoped>
.page.home {
  height: 100vh;

  .home-blocks {
    width: 100%;
    display: flex;
    height: 100%;

    .form-block {
      width: 40%;
      background-color: #352E4F;
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 5%;

      .form-container {
        display: flex;
        justify-content: center;
        width: 100%;
        flex-direction: column;

        h1,
        h2 {
          font-weight: bold;
          margin-bottom: 35px;
          text-align: left
        }

        form {
          width: 100%;
          display: flex;
          flex-direction: column;
          gap: 20px;

          .email-notif {
            color: rgb(140, 228, 140);
            font-family: 'Raleway';
            font-size: 1.25rem
          }

          #form-btn {
            background-color: white;
            color: black;
            font-weight: bold;
            text-transform: uppercase;

            &:hover {
              cursor: pointer;
              background-color: black;
              border: 2px solid black;
              color: white;
              transition: all 250ms ease;
            }
          }

          .form-name {
            display: flex;
            gap: 20px;
          }

          textarea:focus,
          input:focus {
            outline: none;
          }

          input,
          textarea {
            width: 100%;
            background-color: #352E4F;
            border: none;
            border: 2px solid white;
            padding: 15px;
            font-family: 'Raleway';
            letter-spacing: 2px;
            color: white;

            input {
              height: 50px;
            }

            &::placeholder {
              color: rgba(255, 255, 255, 0.411);
              text-transform: uppercase;
              font-family: 'Raleway';
              letter-spacing: 2px;
            }
          }
        }
      }
    }

    .img-block {
      width: 60%;
      flex-grow: 1;
      background-color: black;
      // background-image: url('@/assets/images/logos/dnz.png');
      background-position: center center;
      background-size: contain;
      background-repeat: no-repeat;
      overflow: hidden;
      position: relative;

      .inventory-items {
        display: flex;
        width: 100%;
        height: 100%;
        gap: 20px;
        flex-wrap: wrap;
        overflow-y: scroll;
        padding: 35px;

        .item {
          width: 30%;
          max-width: 30%;
          flex-grow: 1;
          background-color: white;
          padding: 15px;
          font-family: 'Raleway';

          .item-content {
            height: 100%;
            display: flex;
            flex-direction: column;
            justify-content: space-between;

            .price {
              font-weight: bold;
            }

            .description {
              margin-top: 10px;
              margin-bottom: 10px;
              font-size: 0.8rem;
            }
          }
        }
      }

      .order-tab {
        width: 100%;
        height: 100%;
        top: 0;
        background-color: white;
        position: absolute;
        transform: translateX(60vw);
        transition: all 350ms ease-out;
        padding: 50px;

        .invoice-product {
          display: flex;
          justify-content: space-between;
          gap: 25px;
          font-family: 'Raleway';

          .product-prices {
            display: flex;
            gap: 25px;
          }
        }

        .customer-details {
          font-family: 'Raleway';
          margin-top: 35px;

          .customer_firstname,
          .customer_lastname {
            font-weight: 700;
            font-size: 1.25rem;
          }
        }

        h1 {
          color: black;
          text-align: left;
        }

        .customer-details {
          margin-bottom: 50px;
        }

        &.open {
          transform: translateX(0vw);
        }
      }
    }
  }

  .full-width {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100%;

    .logo {
      max-width: 200px;
    }
  }

  h1 {
    margin-top: 32px;
    margin-bottom: 16px;
    color: var(--white);
    text-align: center;
  }

  h2 {
    color: var(--white);
    font-weight: 200;
  }
}
</style>
