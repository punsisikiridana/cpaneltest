<template>
  <div>
    <div class="items">
      <div id="preloader" class="row grid-conatainer" v-if="isLoading">
        <div
          class="col-md-3 col-lg-3 custom-column-xl main-dashboard"
          v-for="i in 24"
          v-bind:key="i"
        >
          <div class="outside-nft border-on-profile">
            <div class="inside-nft">
              <div class="inner-outside-nft">
                <div class="inner-nft">
                  <div class="item-main">
                    <div class="item-head">
                      <div class="preloader-img"></div>
                    </div>
                  </div>

                  <div class="item-img"></div>

                  <div class="display-flex -mt-15">
                    <div class="preloader-content"></div>
                  </div>
                  <div class="text-center currency-label">
                    <div class="preloader-content"></div>
                  </div>
                  <div class="preloader-half">
                    <div class="preloader-content"></div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div :id="div_id" class="row grid-conatainer">
      <div
        v-for="(collectible, index) in collectibles"
        :key="index"
        class="col-md-3 col-lg-3 custom-column-xl main-dashboard"
      >
        <div class="outside-nft">
          <div class="inside-nft">
            <div class="inner-outside-nft">
              <div class="inner-nft">
                <div class="item-main">
                  <div class="item-head">
                    <div class="legend">
                      <div
                        :class="
                          collectible.legend.toString().toLowerCase().trim()
                        "
                      >
                        <i :class="collectible.icon"></i>
                        {{ collectible.legend }}
                      </div>
                    </div>

                    <div
                      v-if="
                        current_user != collectible.owner_id &&
                        filter != 'created'
                      "
                      class="item-menu"
                    >
                      <i class="fas fa-ellipsis-h"></i>
                    </div>

                    <div
                      v-if="
                        current_user == collectible.owner_id &&
                        current_page == 'profile' &&
                        filter != 'created'
                      "
                      class="action-menu"
                    >
                      <i class="fas fa-ellipsis-h"></i>
                    </div>
                  </div>

                  <div class="item-menu-drop d-none">
                    <a
                      v-if="collectible.isp == 1 && collectible.is_selling == 1"
                      :id="collectible.slug"
                      class="buy-now"
                      href="javascript:void(0)"
                      @click="fetchSingleNft(collectible, 'checkout')"
                      >Buy now</a
                    >
                    <a
                      v-if="collectible.biddingStatus"
                      :id="collectible.slug"
                      class="place-bid"
                      href="javascript:void(0)"
                      @click="fetchSingleNft(collectible, 'bid')"
                      >Place a bid</a
                    >
                    <a
                      :id="collectible.slug"
                      class="report"
                      href="javascript:void(0)"
                      @click="fetchSingleNft(collectible, 'report')"
                      >Report</a
                    >
                  </div>

                  <div
                    :id="collectible.name"
                    class="profile-action-menu-drop d-none"
                  >
                    <!--   <div v-if="collectible.isp == 1" class="input-group mb-3">
                      <div class="input-group-prepend">
                        <div class="input-group-text">
                          <input
                            :id="'nft-' + collectible.slug"
                            type="checkbox"
                            aria-label="Checkbox for following text input"
                            @change="
                              customUpdateNft(
                                collectible.slug,
                                collectible.owner_id
                              )
                            "
                            :checked="collectible.is_selling == 1"
                          />
                        </div>
                      </div>
                     <input
                        type="text"
                        class="form-control"
                        aria-label="Text input with checkbox"
                        value="Put on sale"
                        @click="putOnSale(collectible)"
                      /> 
                    </div>-->
                    <a
                      type="button"
                      :id="collectible.slug"
                      class="sale"
                      @click="putOnSale(collectible)"
                      v-if="collectible.is_selling != 1"
                      >Put On Sale</a
                    >
                    <div
                      class="input-group-prepend"
                      v-if="collectible.is_selling"
                    >
                      <div class="input-group-text">
                        <input
                          :id="'nft-' + collectible.slug"
                          type="checkbox"
                          aria-label="Checkbox for following text input"
                          v-model="checked"
                        />
                      </div>
                      <a
                        :id="collectible.slug"
                        class="sale"
                        @click="checked ? remove(collectible) : null"
                        >Remove From Marketplace</a
                      >
                    </div>
                  </div>
                </div>
                <div></div>
                <div class="item-img">
                  <img
                    v-if="collectible.fileType == 'image'"
                    :src="collectible.file"
                    alt=""
                  />
                  <video
                    v-if="
                      collectible.fileType == 'video' ||
                      collectible.fileType == 'audio'
                    "
                    autoplay
                    loop
                    muted
                  >
                    <source :src="collectible.file" type="video/mp4" />
                  </video>
                </div>

                <div class="display-flex">
                  <div
                    v-if="collectible.isp == 1 && collectible.is_selling == 1"
                    class="text-center currency-amount"
                  >
                    {{ collectible.price }}{{ collectible.currencyName }}
                  </div>
                  <div v-else class="text-center currency-amount">
                    Not for sale
                  </div>

                  <span v-if="filter != 'created'" class="copies"
                    >{{ collectible.ownedCopies }} of
                    {{ collectible.copies }}</span
                  >
                </div>
                <div class="text-center currency-label">
                  {{ collectible.name }}
                </div>
                <a
                  :href="
                    show_collectible +
                    collectible.contract +
                    ':' +
                    collectible.id +
                    ':' +
                    collectible.owner_id
                  "
                  class="btn viewBtn"
                  >VIEW</a
                >
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <checkout-modal-component
      :singleNft="singleNft"
      :page="current_page"
      :current_user="current_user"
    ></checkout-modal-component>
    <put-on-sale-modal-component
      v-if="loaded"
      :singleNft="singleNft"
      :page="current_page"
    >
    </put-on-sale-modal-component>
    <bid-modal-component
      :singleNft="singleNft"
      :page="current_page"
      :bidOpen="open"
    ></bid-modal-component>
    <report-modal-component :singleNft="singleNft"></report-modal-component>
    <bid-list-modal-component
      v-if="current_page == 'profile'"
      :bidList="bidList"
      :collectible="bidListNFT"
      :page="current_page"
    ></bid-list-modal-component>
  </div>
</template>

<script>
import { removeSale } from "./../data";
import PutOnSaleModalComponent from "./modals/PutOnSaleModalComponent.vue";
export default {
  components: { PutOnSaleModalComponent },
  props: [
    "div_id",
    "collectible_asset",
    "show_collectible",
    "current_user",
    "base_url",
    "collectibles",
    "page",
    "filter",
    "isLoading",
  ],
  data() {
    return {
      singleNft: {},
      current_page: "",
      bidList: [],
      bidListNFT: "",
      checked: false,

      loaded: false,
      open: false,

      //collectible: this.collectibles[0],
    };
  },
  watch: {
    singleNft: function () {
      this.price = +parseFloat(this.singleNft.total).toFixed(2);
      this.nft_id = this.singleNft.id;
      this.record_id = this.singleNft.record_id;
    },
  },
  methods: {
    putOnSale(collectible) {
      const _this = this;
      _this.singleNft = collectible;
      _this.loaded = true;
      _this.toggleModal("putOnSale");
    },
    async remove(collectible) {
      const _this = this;
      const res = await removeSale(collectible.db_id);
    },
    capitalizeFirstLetter(string) {
      return string.charAt(0).toUpperCase() + string.slice(1);
    },
    fetchSingleNft(collectible, clicked) {
      if (clicked == "bid") {
        this.open = true;
      }
      if (this.current_user == 0) {
        window.location.href = this.base_url + "/connect";
      } else {
        this.toggleModal(clicked);
      }
      this.singleNft = collectible;
      this.singleNft.total = this.fetchTotal(collectible.price);
      this.singleNft.currency = this.fetchCurrency(collectible.currency);
      this.singleNft.max = this.fetchTotalCopies(collectible.copies);
    },
    fetchTotal(price) {
      //return parseFloat(price.split(" ")[0]).toFixed(2);
      return this.singleNft.price;
    },
    fetchCurrency(price) {
      //return price.split(" ")[1];
      return this.singleNft.currency;
    },
    fetchTotalCopies(copies) {
      //return copies.split(" ")[0];
      return this.singleNft.copies;
    },
    customUpdateNft(slug, user_id) {
      axios
        .get("/update/nft/status/" + slug + "/" + user_id)
        .then((res) => {
          this.$parent.refreshAfterUpdate();
        })
        .catch((error) => {
          console.log(error);
        });
    },
    fetchBids(collectible, clicked) {
      axios
        .get("/bid-list/" + collectible.record_id)
        .then((res) => {
          this.bidListNFT = collectible;
          this.bidList = res.data.list;
          this.toggleModal(clicked);
        })
        .catch((error) => {
          console.log(error);
        });
    },
    updateBidList(collectible) {
      axios
        .get("/bid-list/" + collectible.record_id)
        .then((res) => {
          this.bidListNFT = collectible;
          this.bidList = res.data.list;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    toggleModal(clicked) {
      modalOpen($("#" + clicked + "Modal"), $("." + clicked + "-content"));
    },
  },
  mounted() {
    this.current_page = this.page;
  },
};
</script>

<style>
.item-description {
  font-size: 16px;
}

.grid-conatainer {
  display: grid;
  grid-template-columns: repeat(auto-fill, 250px);
  grid-gap: 30px;
  margin-top: 50px;
}
</style>