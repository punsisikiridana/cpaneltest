<template>
  <div>
    <div class="details-tab">
      <ul class="nav nav-tabs">
        <li>
          <a href="#home" @click="detailsActive()" class="tabLink">Details</a>
        </li>
        <li>
          <a href="#holder" @click="holderActive()" class="tabLink">Holder</a>
        </li>

        <li class="active">
          <a href="#past_transactions" @click="bidsActive()" class="tabLink"
            >Bids</a
          >
        </li>
      </ul>
      <div class="tabGroup">
        <div
          id="home"
          class="tab-pane"
          v-bind:class="{ 'tab-active': home_active }"
        >
          <div class="row dtab">
            <div class="col-3 col-md-2">
              <div class="inlineDiv">
                <a :href="user_profile + '/' + creator.wallet">
                  <img class="br-50" :src="creator.display_photo" width="50" />
                </a>
                <i class="fa fa-check-circle imgCheck" aria-hidden="true"></i>
              </div>
            </div>
            <div class="col-9 col-md-10">
              <label class="position">Designer</label>
              <label class="positionHolder"
                ><a :href="user_profile + '/' + creator.wallet">{{
                  creator.name
                }}</a></label
              >
            </div>
          </div>

          <div class="row dtab">
            <div class="col-3 col-md-2">
              <div class="inlineDiv">
                <a :href="user_profile + '/' + current_owner.wallet">
                  <img
                    class="br-50"
                    :src="current_owner.display_photo"
                    width="50"
                  />
                </a>
                <i class="fa fa-check-circle imgCheck" aria-hidden="true"></i>
              </div>
            </div>
            <div class="col-9 col-md-10">
              <label class="position">Holder</label>
              <label class="positionHolder"
                ><a :href="user_profile + '/' + current_owner.wallet">{{
                  current_owner.name
                }}</a></label
              >
            </div>
          </div>

          <div class="row dtab">
            <div class="col-3 col-md-2">
              <div class="inlineDiv">
                <a :href="asset_url + 'collection/' + collection.address">
                  <img class="br-50" :src="collection.icon" width="50" />
                </a>
                <i class="fa fa-check-circle imgCheck" aria-hidden="true"></i>
              </div>
            </div>
            <div class="col-9 col-md-10">
              <label class="position">Collection</label>
              <label class="positionHolder"
                ><a :href="asset_url + 'collection/' + collection.address">{{
                  collection.name
                }}</a></label
              >
            </div>
          </div>
        </div>

        <div
          id="holder"
          class="tab-pane"
          v-bind:class="{ 'tab-active': holder_active }"
        >
          <div v-for="(owner, index) in owners" :key="index" class="row dtab">
            <div class="col-3 col-md-2">
              <div class="inlineDiv">
                <a :href="user_profile + '/' + owner.wallet">
                  <img class="br-50" :src="owner.display_photo" width="50" />
                </a>
                <i class="fa fa-check-circle imgCheck" aria-hidden="true"></i>
              </div>
            </div>
            <div class="col-9 col-md-10">
              <label class="position"
                >{{ owner.ownedCopies }} of {{ collectible.copies }}</label
              >
              <label class="positionHolder"
                ><a :href="user_profile + '/' + owner.wallet">{{
                  owner.name
                }}</a></label
              >
            </div>
          </div>
        </div>

        <div
          id="past_transactions"
          class="tab-pane"
          v-bind:class="{ 'tab-active': bid_active }"
        >
          <button
            type="submit"
            class="btn btn-success"
            v-if="!biddingStatus && owner"
            @click="startBid()"
          >
            Open Token for Bidding
          </button>
          <button
            type="submit"
            class="btn btn-danger"
            v-if="biddingStatus && owner"
            @click="endBid()"
          >
            Remove from Bidding
          </button>
          <button
            type="submit"
            class="btn btn-success"
            v-if="biddingStatus && owner && haveBids"
            @click="acceptBidding()"
          >
            Accept Highest Bid
          </button>
       
          <h5   v-if="biddingStatus && haveBids">Highest Bid</h5>
          <div class="row"   v-show="biddingStatus && haveBids">
          <div class="col-3 col-md-2">
              <div class="inlineDiv">
                <a :href="user_profile + '/' + this.highestBid.maxBidder">
                  <img class="br-50" :src="highestBid.proPic" width="50" />
                </a>
                <i class="fa fa-check-circle imgCheck" aria-hidden="true"></i>
              </div>
            </div>

               <div class="col-9 col-md-10">
              <label class="position"
                >
                <span class="positionHolder">{{ this.highestBid.maxAmount/10**18}}</span> {{ this.highestBid.maxBidToken}}
                on {{ this.highestBid.maxBidTime.slice(0, 10) }} by
                <a :href="user_profile + '/' + this.highestBid.maxBidder"
                  ><span class="positionHolder">{{
                    this.highestBid.maxBidder
                  }}</span></a
                >
              </label>

            </div>
            </div>
            <br><br>
          <h5 v-if="biddingStatus && haveBids">All Bids</h5>
          <!--h5 v-if="biddingStatus==0">Not available for Bidding</h5-->
          <div
            v-for="(transac, index) in allBids"
            :key="index"
            class="row dtab"
            v-show="biddingStatus"
          >
            <div class="col-3 col-md-2">
              <div class="inlineDiv">
                <a :href="user_profile + '/' + transac.bidding_address">
                  <img class="br-50" :src="transac.proPic" width="50" />
                </a>
                <i class="fa fa-check-circle imgCheck" aria-hidden="true"></i>
              </div>
            </div>
            <div class="col-9 col-md-10">
              <label class="position"
                > 
                <span class="positionHolder">{{ transac.bidding_amount }}</span> {{ transac.bidding_token }}
                on {{ transac.created_at.slice(0, 10) }} by
                <a :href="user_profile + '/' + transac.bidding_address"
                  ><span class="positionHolder">{{
                    transac.bidding_address
                  }}</span></a
                >
              </label>

            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import {
  getHighestBid,
  getBiddingStatus,
  getAllBids,
  startBidding,
  endBidding,
  getConnectedAddress,
  acceptBid,
} from ".././../bidFunc";
import { toAddress, checkConnection} from ".././../etherFunc";
export default {
  props: [
    "creator",
    "current_owner",
    "owners",
    "transactions",
    "user_profile",
    "collectible",
    "asset_url",
    "collection",
    "collection_image",
    "collection_url",
  ],
  data() {
    return {
      //asset_url :  "https://cdn.pixabay.com/photo/2017/06/13/12/53/profile-2398782_1280.png",
      allBids: {},
      bid_active: false,
      home_active: true,
      holder_active: false,
      biddingStatus: false,
      haveBids:false,
      owner: false,
      highestBid: {
        maxBidder:'',
        maxBidAmount:0,
        maxBidder:'',
        maxBidTime:'',
        maxBidToken:''
      },
      address: {},
    };
  },
  async mounted() {
      this.loadData();
  },
  methods: {

async loadData(){

    //var highestBid = await getHighestBid(this.current_owner.wallet, this.collectible.contract,this.collectible.id);
    this.address = await getConnectedAddress();
    var allBids = await getAllBids(
      this.current_owner.wallet,
      this.collectible.contract,
      this.collectible.id
    );
    var output = await getBiddingStatus(
      this.current_owner.wallet,
      this.collectible.contract,
      this.collectible.id
    );
    var res =  await getHighestBid(
      this.current_owner.wallet,
      this.collectible.contract,
      this.collectible.id
    );
    if(res!=false){
      this.highestBid = res;
      this.haveBids = true;
    }
    else{
      this.haveBids = false;
    }
    this.biddingStatus = output;
    this.allBids = allBids;
    if (this.address == this.current_owner.wallet) {
      this.owner = true;
    }
    console.log(res)
},

    detailsActive() {
      this.home_active = true;
      this.holder_active = false;
      this.bid_active = false;
    },
    holderActive() {
      this.home_active = false;
      this.holder_active = true;
      this.bid_active = false;
    },
    bidsActive() {
      this.home_active = false;
      this.holder_active = false;
      this.bid_active = true;
    },

    async startBid() {
      var res = await startBidding(
        this.current_owner.wallet,
        this.collectible.contract,
        this.collectible.id
      );
  
     if(res ==1){
 this.biddingStatus = true;
     }
     this.loadData();
      
    },
   async endBid() {
      var res = await endBidding(
        this.current_owner.wallet,
        this.collectible.contract,
        this.collectible.id
      );
    if(res ==1){
  this.biddingStatus = false;
    }
    this.loadData();
     
    },

    async acceptBidding() {

      var res = await acceptBid(
     this.collectible.type== 721?true:false,
     this.collectible.contract,
     this.collectible.id,
     this.highestBid.maxBidToken,
     this.highestBid.maxAmount,
     this.highestBid.maxBidder,
     this.highestBid.maxBidSig,
     this.highestBid.maxBidSalt
      );
      console.log(res);
    },
  },
};
</script>