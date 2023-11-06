<template>
  <form v-show="visible">
    <label>First Name</label>
    <input id="fname" type="text" v-model="contact.firstname">
    <label>Last Name</label>
    <input id="lname" type="text" v-model="contact.lastname">
    <div id="email">
      <label>Email ({{ validatedEmail }})</label>
      <input type="text" v-for="(addr, index) in contact.email" v-model="contact.email[index]" :key="index">
      <button id="btnAddEMail" @click.prevent="addEmail">Add email</button>
    </div>
    <div id="phone">
      <label>Phone ({{ validatedPhone }})</label>
      <input type="text" @keyup="purgeNumber(index)" v-for="(number, index) in contact.phoneNumber" v-model="contact.phoneNumber[index]" :key="index">
      <button id="btnAddNumber" @click.prevent="addPhone">Add Phone</button>
    </div>
    <div>
      <button id="btnSubmit" @click.prevent="submit">Submit</button>
      <button id="btnDelete" v-show="!newCon" @click.prevent="removeContact">Delete Contact</button>
      <button id="btnCancel" @click.prevent="cancel">Cancel</button>
    </div>
  </form>
</template>

<script>
/**
 * Event bus for contact
 */
import { ContactBus } from '../bus/ContactBus'

export default {
  name: 'ContactForm',
  props: {
    contact: {
      type: Object,
      required: true
    },
    visible: {
      type: Boolean
    },
    newCon: {
      type: Boolean
    },
    index: {
      type: [Number, String]
    }
  },
  computed: {
    validatedEmail: function () {
      /**
       * Returns false if any email
       * in contact.email array is invalid
       * and true otherwise
       */
      const regex = /[A-Z0-9._%+-]+@[A-Z0-9-]+.+.[A-Z]{2,4}/igm
      return this.contact.email.every((item) => regex.test(item))
    },
    validatedPhone: function () {
      /**
       * Returns false if a phone number
       * in contact.phoneNumber array is
       * invalid and true otherwise
       */
      const regex = /^\(?(\d{3})\)?[-.\s]?(\d{3})[-.\s]?(\d?)(?:[:-\s]\d{4})?$/im
      return this.contact.phoneNumber.every((item) => regex.test(item))
    }
  },
  methods: {
    submit: function () {
      if (this.contact.firstname && this.contact.lastname) {
        if (this.validatedEmail || this.validatedPhone) {
          ContactBus.$emit('addCon', this.contact)
        } else {
          ContactBus.$emit('noAddCon', [])
        }
      } else {
        ContactBus.$emit('noAddCon', [])
      }
    },
    purgeNumber: function (index) {
      this.contact.phoneNumber[index] = this.contact.phoneNumber[index].replace(/\D/g, '')
    },
    cancel: function () {
      ContactBus.$emit('close', true)
    },
    removeContact: function () {
      ContactBus.$emit('delCon', this.index)
    },
    addEmail: function () {
      /**
       * Appends empty string to the end of
       * contact.email array
       */
      this.contact.email.push('')
      // this.contact[this.index].email.push(this.contact.email[this.index])
    },
    addPhone: function () {
      /**
       * Appends empty string to the end of
       * contact.phoneNumber array
       */
    }
  }
}
</script>

<style scoped>
form {
  margin: 30px;
}
input {
  margin: 20px 10px;
}
</style>
