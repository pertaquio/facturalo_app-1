<template>
  <f7-page class="page-red-white" color="white">
    <f7-toolbar tabbar :position="isBottom ? 'bottom' : 'top'" bg-color="white" color="red" class="no-shadow">
      <f7-link
        href="/"
        icon-md="material:home">
        Inicio
      </f7-link>
      <f7-link
        tab-link-active
        tab-link="#"
        icon-md="material:insert_drive_file"
        class="text-align-center">
        Crear <br> comprobantes
      </f7-link>
      <f7-link
        href="/documents/"
        icon-md="material:view_stream"
        class="text-align-center">
        Listado de <br> comprobantes
      </f7-link>
      <f7-link
        href="/report/"
        icon-md="material:equalizer">
        Reportes
      </f7-link>
    </f7-toolbar>

    <f7-block>
      <f7-row>
        <f7-col width="90">
          <a class="link back text-color-white">
            <i class="icon icon-back"></i>
            <span class="">Nota de Venta</span>
          </a>
        </f7-col>
        <f7-col width="10">
          <f7-link class="panel-open text-color-white text-align-right" open-panel="right" icon="fas fa-bars"></f7-link>
        </f7-col>
      </f7-row>
    </f7-block>

    <f7-popup class="demo-popup" :opened="popupOpened" @popup:closed="popupOpened = false">
      <items-form :showDialog.sync="popupOpened" ref="form_items_car" @addItemsCar="addItems"></items-form>
    </f7-popup>

    <f7-popup
      class="demo-popup"
      :opened="popupCustomerOpened"
      @popup:closed="popupCustomerOpened = false">
      <customer-form
        :codeType="codeType"
        :customerId="form.customer_id"
        :showDialog.sync="popupCustomerOpened"
        ref="form_customer_car"
        @addCustomerCar="addCustomer"
      ></customer-form>
    </f7-popup>

    <f7-card class="card-100 padding-top padding-horizontal no-shadow" color="red">
      <f7-block>
        <form class="list no-hairlines-md" id="demo-form">
          <ul>
            <f7-row>
              <f7-col width="50">
                <div class="item-content item-input">
                  <div class="item-inner">
                    <div class="item-title item-label">Serie</div>
                    <div class="item-input-wrap input-dropdown-wrap">
                      <select v-model="form.series_id" placeholder="Please choose..." >
                        <template v-for="(row, index) in series">
                          <option :value="row.id" :key="index">{{row.number}}</option>
                        </template>
                      </select>
                    </div>
                  </div>
                </div>
              </f7-col>
              <f7-col width="50">
                <div class="item-content item-input">
                  <div class="item-inner">
                    <div class="item-title item-label">Fecha Emisión</div>
                    <div class="item-input-wrap">
                      <input name="date" v-model="form.date_of_issue" type="date" />
                    </div>
                  </div>
                </div>
              </f7-col>
            </f7-row>

            <li>
              <f7-col>
                <f7-card style="font-weight:bold;color:#0f233c;background:#ddd;border-radius: 15px;">
                  <f7-card-content>
                    <f7-row>
                      <f7-col width="15" class="align-self-center">
                        <div class="badge bg-color-white">
                          <f7-icon icon="fas fa-user" size="24" color="blue"></f7-icon>
                        </div>
                      </f7-col>
                      <f7-col width="75" class="text-align-left">
                        <small>CLIENTE</small><br>
                        <small class="no-margin">{{this.form.datos_del_cliente_o_receptor ? this.form.datos_del_cliente_o_receptor.apellidos_y_nombres_o_razon_social : ''}}</small>
                      </f7-col>
                      <f7-col width="10" class="align-self-center">
                        <div class="badge bg-color-white text-align-right color-blue">
                          <f7-link @click="popupCustomerOpened = true" style="color:#0f233c;" icon="fas fa-arrow-right"></f7-link>
                        </div>
                      </f7-col>
                    </f7-row>
                  </f7-card-content>
                </f7-card>
              </f7-col>
            </li>

            <li style="margin-bottom: 5%;" class="item-content item-input">
              <br />
              <div class="data-table">
                <table>
                  <thead>
                    <tr>
                      <th class="label-cell">#</th>
                      <th class="numeric-cell">Descripcion</th>
                      <th class="medium-only">Cantidad</th>

                      <th class="medium-only">Precio</th>
                      <th class="medium-only">Subtotal</th>
                      <th class="medium-only">Total</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="(row, index) in form.items" :key="index">
                      <td class="label-cell">{{index + 1 }}</td>
                      <td class="numeric-cell">{{row.item.description}}</td>
                      <td class="numeric-cell">{{row.quantity}}</td>

                      <td class="numeric-cell">S/. {{ Number(row.unit_price).toFixed(2)}}</td>
                      <td class="numeric-cell">S/. {{row.total_value}}</td>
                      <td class="numeric-cell">S/. {{row.total}}</td>
                    </tr>
                  </tbody>
                </table>
              </div>
            </li>

            <li class="padding-vertical">
              <f7-button @click="popupOpened = true" fill color="blue" class="text-align-left padding-left">
                <small>
                  <f7-icon icon="fas fa-plus"></f7-icon>
                  Añadir producto
                </small>
              </f7-button>
            </li>

            <li class="padding-bottom">
              <f7-card style="font-weight:bold;background:#d61a0c;border-radius: 15px;font-size: 12px;">
                <f7-card-content class="text-color-white">
                  <f7-row>
                    <f7-col width="50" class="padding-left">
                      <h2 class="no-padding no-margin-top" style="margin-bottom: -10px;">{{form.total_taxed}}</h2>
                      <small>OP.GRAVADA</small> <br><br>
                      <h2 class="no-padding no-margin-top" style="margin-bottom: -10px;">{{form.total_igv}}</h2>
                      <small>IGV</small>
                    </f7-col>
                    <f7-col width="50" class="align-self-center">
                      <h1 class="no-padding no-margin">{{form.total}}</h1>
                      TOTAL
                    </f7-col>
                  </f7-row>
                </f7-card-content>
              </f7-card>
              <f7-row class="btn-actions">
                <f7-col width="10" class="">
                  <f7-button raised fill color="gray"  @click="cancel">
                    <f7-icon icon="fas fa-trash"></f7-icon>
                  </f7-button>
                </f7-col>
                <f7-col width="80" class="">
                </f7-col>
                <f7-col width="10" class="">
                  <f7-button raised fill color="green"  @click="send">
                    <f7-icon icon="fas fa-check"></f7-icon>
                  </f7-button>
                </f7-col>
              </f7-row>
            </li>
          </ul>
        </form>
      </f7-block>
    </f7-card>
  </f7-page>
</template>

<style scoped>

.navbar-cus{
   background:#17a2b8;
   color:white
}
.m-text {
  text-align: left;
  font-size: 12px;
  font-weight: bold;
}
.m-text-r {
  text-align: center;
}
.footer-text {
  position: absolute;
  margin-top: 2%;
  width: 50%;
  padding-left: 1%;
}
.footer-data {
  width: 50%;
  color: #fff;
  background: #17a2b8;
  margin: auto;
  border-right: 30px solid #fff;
  border-left: 30px solid #fff;
  border-bottom: 73px solid transparent;
  text-align: center;
}
.footer {
  text-align: center;
  z-index: 9999;
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
  color: white;
  text-align: center;
}
</style>
<script>
import moment from "moment";
import _ from "lodash";
import ItemsForm from "components/document/ItemsForm";
import CustomerForm from "components/document/CustomerForm";
import { auth } from "mixins_/auth";

export default {
  name: "FormSaleNote",
  components: { ItemsForm, CustomerForm },
  mixins: [auth],
  data: function() {
    // Must return an object
    return {
      codeType: "",
      isBottom: true,
      popupCustomerOpened: false,
      search_item: "",
      customers: [],
      form: {},
      popupOpened: false,
      series:[]
    };
  },
  computed: {},
  async created() {
    await this.initForm();
    await this.getTables();
    await this.getSeries()
  },

  methods: {
    async getSeries() {

      const self = this;
      self.$f7.preloader.show();

      await this.$http.get(`${this.returnBaseUrl()}/sale-note/series`, this.getHeaderConfig()).then(response => {
          this.series = response.data;
          this.form.series_id = (this.series.length > 0) ? this.series[0].id : null
        })
        .catch(err => {})
        .then(() => {
          self.$f7.preloader.hide();
        })

    },
    addCustomer(row) {
      this.popupCustomerOpened = false;
      this.form.customer_id = row.id;
      this.form.datos_del_cliente_o_receptor = {
        codigo_tipo_documento_identidad: row.identity_document_type_id,
        numero_documento: row.number,
        apellidos_y_nombres_o_razon_social: row.name,
        codigo_pais: row.country_id,
        ubigeo: row.district_id ? row.district_id : '150101',
        direccion: row.address,
        correo_electronico: row.email,
        telefono: row.telephone
      };
    },
    addItems(rows) {
      let contex = this
      contex.popupOpened = false;
      rows.forEach(record => {
        contex.form.items.push(record)
      });
      this.calculateTotal();
    },
    cancel() {
      this.initForm();
      this.$f7router.navigate("/");
    },

    validate() {
      const self = this;

      if (!this.form.series_id) {
        self.$f7.dialog.alert(`Debe seleccionar una serie.`, "Mensaje");
        return false;
      }

      if (this.form.items.length == 0) {
        self.$f7.dialog.alert(`Debe agregar productos.`, "Mensaje");

        return false;
      }

      if (!this.form.customer_id) {
        self.$f7.dialog.alert(
          `Debe seleccionar un cliente.`,
          "Mensaje"
        );

        return false;
      }

      return true;
    },

    send() {
      const self = this;

      let valid = this.validate();

      if (!valid) return;

      self.$f7.preloader.show();

      this.$http
        .post(`${this.returnBaseUrl()}/sale-note`, this.form, this.getHeaderConfig())
        .then(response => {
          let data = response.data;
          if (data.success) {
            this.initForm();

            self.$f7.dialog.alert(
              `Nota de venta registrada`,
              "Mensaje"
            );
            self.$f7router.navigate("/documents/");
          } else {
            alert("No se registro la Nota de venta");
          }
        })
        .catch(err => {
          alert(`${err.message}`);
        })
        .then(() => {
          self.$f7.preloader.hide();
        });
    },

    calculateTotal() {
      let total_discount = 0;
      let total_charge = 0;
      let total_exportation = 0;
      let total_taxed = 0;
      let total_exonerated = 0;
      let total_unaffected = 0;
      let total_free = 0;
      let total_igv = 0;
      let total_value = 0;
      let total = 0;
      let total_plastic_bag_taxes = 0;
      this.form.items.forEach(row => {
        if (row.affectation_igv_type_id === "10") {
          total_taxed += parseFloat(row.total_value);
        }
        if (row.affectation_igv_type_id === "20") {
          total_exonerated += parseFloat(row.total_value);
        }
        if (row.affectation_igv_type_id === "30") {
          total_unaffected += parseFloat(row.total_value);
        }
        if (row.affectation_igv_type_id === "40") {
          total_exportation += parseFloat(row.total_value);
        }
        if (["10", "20", "30", "40"].indexOf(row.affectation_igv_type_id) < 0) {
          total_free += parseFloat(row.total_value);
        }
        if (
          ["10", "20", "30", "40"].indexOf(row.affectation_igv_type_id) > -1
        ) {
          total_igv += parseFloat(row.total_igv);
          total += parseFloat(row.total);
        }
        total_value += parseFloat(row.total_value);
        total_plastic_bag_taxes += parseFloat(row.total_plastic_bag_taxes);
      });

      this.form.total_exportation = _.round(total_exportation, 2);
      this.form.total_taxed = _.round(total_taxed, 2);
      this.form.total_exonerated = _.round(total_exonerated, 2);
      this.form.total_unaffected = _.round(total_unaffected, 2);
      this.form.total_free = _.round(total_free, 2);
      this.form.total_igv = _.round(total_igv, 2);
      this.form.total_value = _.round(total_value, 2);
      this.form.total_taxes = _.round(total_igv, 2);
      this.form.total = _.round(total, 2);
    },

    initForm() {
      this.form = {
        prefix: "NV",
        series_id: null,
        establishment_id: null,
        date_of_issue: moment().format("YYYY-MM-DD"),
        time_of_issue: moment().format("HH:mm:ss"),
        customer_id: null,
        currency_type_id: "PEN",
        purchase_order: null,
        exchange_rate_sale: 0,
        total_prepayment: 0,
        total_charge: 0,
        total_discount: 0,
        total_exportation: 0,
        total_free: 0,
        total_taxed: 0,
        total_unaffected: 0,
        total_exonerated: 0,
        total_igv: 0,
        total_base_isc: 0,
        total_isc: 0,
        total_base_other_taxes: 0,
        total_other_taxes: 0,
        total_taxes: 0,
        total_value: 0,
        total: 0,
        operation_type_id: null,
        items: [],
        charges: [],
        discounts: [],
        attributes: [],
        guides: [],
        payments: [],
        additional_information: null,
        actions: {
          format_pdf: "a4"
        },
        apply_concurrency: false,
        type_period: null,
        quantity_period: 0,
        automatic_date_of_issue: null,
        enabled_concurrency: false
      };

      this.form.series_id = (this.series.length > 0) ? this.series[0].id : null

    },
    getTables() {
      const self = this;
      self.$f7.preloader.show();
      this.$http
        .get(
          `${this.returnBaseUrl()}/document/customers`,
          this.getHeaderConfig()
        )
        .then(response => {
          let source = response.data.data;
          self.customers = source.customers;
        })
        .catch(err => {
          console.log(err);
        })
        .then(() => {
          self.$f7.preloader.hide();
        });
    }
  }
};
</script>
