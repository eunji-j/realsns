<template>
  <div class="offset-md-2 col-md-8" data-aos="fade-up">
    <div class="row mb-5">
        <div class="col-12">
            <h2 class="text-white mb-4">Edit Content</h2>
        </div>
    </div>
    <div class="offset-md-1 col-md-10">
        <div class="write-scroll all-scroll pos-relative mt-50">
            <div class="swiper-scrollbar"></div>
            <div class="write-swiper swiper-container oflow-visible" data-slide-effect="flip" data-autoheight="false" data-wheel-control="true" 
                                    data-swiper-speed="200" data-swiper-margin="25" data-swiper-slides-per-view="1"
                                    data-swiper-breakpoints="true" data-scrollbar="true" data-swiper-loop="false"
                                    data-swpr-responsive="[1, 2, 1, 2]">
                <div class="swiper-wrapper">
                    <div class="img-fluid swiper-slide" v-for="(img, index) in imgs" :key="index">
                        <div style="text-align:right; background-color:black;">
                            <i @click="deleteImage($event, index)" class="icon-close text-white"></i>
                        </div>
                        <div :class="img.filter">
                            <img :src = img.base64 class="img-fluid">
                        </div>
                    </div>
                </div>
                <div class="swiper-pagination"  slot="pagination"></div>
                <div class="swiper-button-prev" slot="button-prev"></div>
                <div class="swiper-button-next" slot="button-next"></div>
            </div>
        </div>
        <div class="container col-md-12 px-0 mt-4">
            <div class="btn-group col-12 px-0" role="group" aria-label="Basic example">
                <input type="button" class="btn btn-outline-light col-sm btnprev p-2" value="추가" @click="goAddImage">
            </div>
        </div>
        <!-- <div class="offset-4 col-4 col-md-4 col-lg-4" style="display:inline-block; text-align:center;">
            <input type="button" value="추가" @click="goAddImage" class="btn btn-info btn-md text-white">
        </div> -->
        <div class="row form-group">                    
            <div class="col-md-12" id="parentItrl">
            <label class="text-white" for="itrlist">Hashtag</label>
            <div id="" style="display:inline-block"></div>
            <input type="subject" autocomplete='off' @keyup.enter.prevent='appendDiv' @keydown.space.prevent='appendDiv' id="interest" class="form-control">
            <div id="itrl"></div>
            </div>
        </div>
        <div class="row form-group mb-5">
            <div class="col-md-12">
            <label class="text-white" for="uintr">Story</label> 
            <textarea name="message" id="message" v-model="intro" cols="30" rows="7" class="form-control" style="resize:none;" placeholder="Write your story"></textarea>
            </div>
        </div>
        
        <div v-if="isLocation&&!isLocationSelect" class="row form-group mb-5">
            <div class="col-md-12">
            <label class="text-white">Location Search</label> 
            <input v-model="searchKeyword"  @keyup.enter="searchLocation" type="text" id="search-location-input" class="form-control">
            <br>
            <input @click="searchLocation" type="button" value="Search" class="btn btn-outline-light btn-block">
            </div>
        </div>
        <div v-if="!isLocation&&!isLocationSelect">
            <input @click="isLocation=!isLocation" type="button" value="위치정보 등록" class="btn btn-outline-light btn-block">
            <br/><br/><br/>
        </div>
        <div v-if="isSearching" class="modal-content search-location-googlemap" style="z-index:99998;">
                    <div class="modal-body" style="text-align:center;">
                        <gmap-map
                        :center="center"
                        :zoom="15"
                        style="width:100%;  height: 400px;"
                        >
                        <gmap-info-window :options="infoOptions" :position="infoWinPos" :opened="infoWinOpen" @closeclick="infoWinOpen=false">
                            <br><h4 style="align:left">{{infoContent.title}}</h4>
                            {{infoContent.text}}<br>
                            <img width="150px" :src="infoContent.img" />
                        </gmap-info-window>

                        <gmap-marker
                            :key="index"
                            v-for="(m, index) in markers"
                            :position="m.position"
                            :title="m.title"
                            :info="m.info"
                            @click="placeClick(m, index)"
                        ></gmap-marker>
                        </gmap-map>
                    </div>
                    <!-- <div class="modal-footer">
                        <button type="button" class="btn btn-success text-white" @click="selectLocation" data-dismiss="modal">선택</button>
                        <button type="button" class="btn btn-danger text-white" data-dismiss="modal">닫기</button>
                    </div> -->
                    <div class="modal-footer d-flex justify-content-end py-2">
                        <div class="d-block">
                            <button type="button" class="btn btn-danger mr-2 py-1 px-3" data-dismiss="modal" @click="selectLocation()">선택</button>
                            <button type="button" class="btn btn-secondary py-1 px-3" data-dismiss="modal">닫기</button>
                        </div>
                    </div>
                </div>

        <div v-if="isLocationSelect" class="row form-group mb-5">
            <div class="col-md-12">
            <label class="text-white">Selected Location</label>
            <input style="text-align:center;" v-model="selectedLocation.name" type="text" class="form-control"><br>
            <input @click="isLocationSelect=!isLocationSelect" type="button" value="다시 위치검색" class="btn btn-outline-light btn-block">
            <br/><br/><br/>
            </div>
        </div>
        
        <!-- <div style="margin-top:1%; margin-left:5%;margin-right:5%; height:50px;">
            <div style="display:inline-block; float :left">
            <input type="button" value="처음으로" @click="goPrev" class="btn btn-primary btn-md text-white">
            </div>
            <div style="display:inline-block; float:right">
            <input type="button" value="다음" @click="goNext" class="btn btn-success btn-md text-white">
            </div>
        </div> -->
        <div class="container col-md-12 px-0 mt-4">
            <div class="btn-group col-12 px-0" role="group" aria-label="Basic example">
            <button type="button" class="btn btn-outline-light col-sm btnprev p-2" @click="goPrev">처음으로</button>
            <button type="button" class="btn btn-outline-light col-sm btnprev p-2" @click="goNext">다음</button>
            </div>
        </div>
        <p id="mapModalBtn" style="display:none;" data-toggle="modal" data-target="#mapModal"></p>
        <div class="modal fade" id="mapModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
            <div class="modal-dialog" role="document">
                
            </div>
        </div>

    </div>
</div>
</template>

<script>
import http from "../http-common"
import axios from 'axios'
import $ from "jquery"
// import store from "../store"
export default {
    name:"updatecontent",
    props:["uimgs","items","prevpage"],
    data(){
        return{
            intro:"",
            filterType:"",
            itrlist:[],
            imgs:[],
            errored:false,
            loading:true,
            isLocation:false,
            isLocationSelect:false,
            isSearching:false,
            searchKeyword:"",
            locationList:[],
            center: { lat: 45.508, lng: -73.587 },
            markers: [],
            places: [],
            res:[],
            infoWinPos : null,
            infoWinOpen : false,
            infoContent : {
                title : null,
                text : null,
                img : null
            },
            currentIdx : null,
            currentPlace: null,
            infoOptions: {
                pixelOffset: {
                width: 0,
                height: -35
                }
            },
            selectedLocation: null,
            swiper_html:"",
        }
    },
    methods:{
        deleteImage(index){
        // deleteImage(event, img, index){
            this.imgs.splice(index, 1);
            if(this.imgs.length==0){
                document.querySelector('.write-scroll').remove();
                return;
            }

            var swiper_wrapper = document.createElement('div');
            swiper_wrapper.classList.add('swiper-wrapper');
            for(var i = 0; i<this.imgs.length; i++){

                
                var xdiv = document.createElement('div');
                xdiv.setAttribute('style', 'text-align:right; background-color:black;');
                var xicon = document.createElement('i');
                xicon.classList.add('icon-close');
                xicon.classList.add('text-white');
                xicon.addEventListener("click", function(i){
                    this.deleteImage(i);
                });
                // xicon.addEventListener("click", this.deleteImage(i));
                // xicon.onclick = this.deleteImage(i);
                xdiv.appendChild(xicon);

                var div = document.createElement('div');
                div.classList.add(this.imgs[i].filter);
                var simg = document.createElement('img');
                simg.classList.add('img-fluid');
                simg.setAttribute('src',this.imgs[i].base64);
                div.appendChild(simg);

                
                var swiper_slide = document.createElement('div');
                swiper_slide.classList.add('img-fluid');
                swiper_slide.classList.add('swiper-slide');
                swiper_slide.id = "slide"+i;
                swiper_slide.appendChild(xdiv);
                swiper_slide.appendChild(div);


                swiper_wrapper.appendChild(swiper_slide);
                // window.console.log(swiper_wrapper);
            }
            // var tt = document.querySelector('.swiper-container');
            var tt = document.querySelector('.write-swiper');
            // var tt = event.target.parentNode.parentNode.parentNode.parentNode;
            // window.console.log(tt);
            // window.console.log(document.querySelector('.swiper-container'));
            // document.querySelector('.swiper-container>.swiper-wrapper').remove();
            document.querySelector('.write-swiper>.swiper-wrapper').remove();
            // event.target.parentNode.parentNode.parentNode.remove();
            // window.console.log(swiper_wrapper);
            tt.appendChild(swiper_wrapper);

            document.querySelector('script[src$="script.js"]').remove()
            document.querySelector('script[src$="swiper.js"]').remove()
            let recaptchaScripta = document.createElement('script')
            recaptchaScripta.setAttribute('type',"text/javascript")
            recaptchaScripta.setAttribute('src', "./theme/js/script.js")
            document.body.appendChild(recaptchaScripta)
            let recaptchaScript = document.createElement('script')
            recaptchaScript.setAttribute('type',"text/javascript")
            recaptchaScript.setAttribute('src', "./theme/js/swiper.js")
            document.body.appendChild(recaptchaScript)
        },
        goAddImage() {
            this.$router.push({
                name: 'addimage', 
                params: {
                fimgs:this.imgs,
                prevpage: this.prevpage,
                items: this.items,
                }
            });
        },
        appendDiv() {
            var colorCode  = "#" + Math.round(Math.random() * 0xffffff).toString(16);
            var div = document.createElement('div');
            var span = document.createElement('span');
            var bold = document.createElement('bold')
            var text = document.getElementById('interest').value;
            if(text==""){
              return;
            }
            // this.itrlist.push(text);
            bold.innerText = text;
            div.style.background=colorCode;
            div.classList.add('roundedge');
            div.classList.add('itrlone');
            div.classList.add('text-white');
            span.classList.add('icon-remove')
            span.addEventListener("click", function(event){
              event.target.parentNode.remove();
            });
            div.appendChild(bold);
            div.appendChild(span);
            document.getElementById('itrl').appendChild(div);
            document.getElementById('interest').value='';
            document.getElementById('interest').focus();
        },
        deleteDiv() {
            var r = document.getElementById('itrl');
            var u = document.getElementById('uitrl');
            document.getElementById('parentUitrl').removeChild(u);
            document.getElementById('parentItrl').removeChild(r);
            var rediv = document.createElement('div');
            var rediv2 = document.createElement('div');
            rediv.id = 'itrl';
            rediv2.id = 'uitrl';
            document.getElementById('parentItrl').appendChild(rediv);
            document.getElementById('parentUitrl').appendChild(rediv2);
            //관심사 리셋되며 배열도 리셋
            this.itrlist.splice(0);
        },
        goPrev() {
            this.$router.push({
                name: 'bio', 
                params: {
                    cid:this.items.content_id,
                }
            });
        },
        goNext() {
            // this.imgs[this.imgs.length-1].filter = this.filterType;
            var itrltemp = document.getElementsByClassName('itrlone');
            this.itrlist = [];
            for(var i=0; i<itrltemp.length; i++){
                this.itrlist[i] = itrltemp[i].innerText;
            }
            if(this.imgs[0].base64==""&&this.intro==""){
                this.$store.commit('setModalText', "내용을 입력해주세요.");
                document.getElementById('modalBtn').click();
            }
            if(this.isLocationSelect){
                http
                    .put("/content/updateContent",{
                    user_id:this.$store.state.user_id,
                    content_id:this.items.content_id,
                    content_val:this.intro,
                    hashtagList:this.itrlist,
                    imageList:this.imgs,
                    lat:this.selectedLocation.position.lat,
                    lng:this.selectedLocation.position.lng,
                    location_name:this.selectedLocation.name,
                    })
                    .then(response => {
                    if (response.data['resmsg'] == "게시물 수정 성공"){
                        this.$store.commit('setModalText', "수정 성공!");
                        document.getElementById('modalBtn').click();
                    }
                    else{
                        this.$store.commit('setModalText', "수정 실패!");
                        document.getElementById('modalBtn').click();
                    }
                    })
                    .catch((error) => {
                        this.errored = true;
                        alert(error);
                    })
                    .finally(() => {
                        this.loading = false
                        this.$router.push({
                            name: 'bio',
                            params: {
                            cid: this.items.content_id,
                            }
                        })
                    
                    });
            }
            else{
                http
                  .put("/content/updateContent",{
                    content_id:this.items.content_id,
                    content_val:this.intro,
                    hashtagList:this.itrlist,
                    imageList:this.imgs,
                  })
                  .then(response => {
                    if (response.data['resmsg'] == "게시물 수정 성공"){
                        this.$store.commit('setModalText', "수정 성공!");
                        document.getElementById('modalBtn').click();
                    }
                    else{
                        this.$store.commit('setModalText', "수정 실패!");
                        document.getElementById('modalBtn').click();
                    }
                  })
                  .catch((error) => {
                      this.errored = true;
                      alert(error);
                  })
                  .finally(() => {
                      this.loading = false
                        this.$router.push({
                            name: 'bio',
                            params: {
                            cid: this.items.content_id,
                            }
                        })
                    
                    });

            }
        },

        searchLocation(){
            // document.querySelector("#mapModalBtn").click();
            this.isSearching = true;
            let keyword = this.searchKeyword;
            keyword.replace(" ", "+");

            const url = "http://192.168.100.41:5000/location/"+keyword;
            
            axios.get(url)
                .then((response)=>{
                    this.locationList = response.data.results;
                    this.center.lat = parseFloat(this.locationList[0].geometry.location.lat);
                    this.center.lng = parseFloat(this.locationList[0].geometry.location.lng);

                    this.locationList.forEach(element => {
                        let addr = "";
                        document.querySelector(".search-location-googlemap").scrollIntoView(false);
                        axios.post("https://translation.googleapis.com/language/translate/v2?key=AIzaSyAcnkt6IBUt-bGIMw4u-VEIYpesgw4-2Lk",{
                          "q": [element.formatted_address],
                          "target": "ko"
                        })
                            .then((response)=>{
                                addr = response.data.data.translations[0].translatedText;
                                this.markers.push({
                                    position : {
                                        lat: parseFloat(element.geometry.location.lat),
                                        lng: parseFloat(element.geometry.location.lng)
                                    },
                                    title : element.name,
                                    info : {
                                        title: element.name,
                                        text: addr
                                    }
                                })
                            })
                            .catch((error)=>{
                              alert(error)
                            })
                    })
                })
                .catch((error)=>{
                    alert(error)
                })
        },

        placeClick(marker, index){
          this.infoWinPos = marker.position;
          this.infoContent = marker.info;

          if(this.currentIdx == index){
              this.infoWinOpen = !this.infoWinOpen;
          }else{
              this.infoWinOpen = true;
              this.currentIdx = index;
              this.selectedLocation = {
                  position: marker.position,
                  name: marker.title,
                  addr: marker.info.text
              };
          }
      },

      selectLocation(){
          this.isSearching = false;
          if(this.selectedLocation != null){
              this.isLocationSelect = true;
          }else{
              this.isLocation = false;
          }
      }

    },
    created(){
        // console.log(document.getElementsByClassName("all-scroll")[1]);
        // this.swiper_html = document.getElementsByClassName("all-scroll")[1];
    },
    mounted(){
        // console.log(this.items)
        this.intro = this.items.content_val;
        this.itrlist = this.items.hashtagList;
        if(this.uimgs===undefined){
            this.imgs = this.items.imageList;
        }
        else if(this.uimgs.length>0){
            this.imgs = this.uimgs;
        }
        // console.log(this.uimgs)
        // console.log(this.imgs)
        // console.log(this.items.imageList)
        if(this.items.location_name!=null){
            // this.selectedLocation.name = this.items.location_name;
            this.selectedLocation = {
                  position: {lat:this.items.lat, lng:this.items.lng},
                  name: this.items.location_name,
                //   addr: marker.info.text
            };
            // this.selectedLocation.position.lat = this.items.lat;
            // this.selectedLocation.position.lng = this.items.lng;
            this.isLocation = true;
            this.isLocationSelect = true;
        }
        $('html').scrollTop(0);
        document.querySelector('script[src$="script.js"]').remove()
        document.querySelector('script[src$="swiper.js"]').remove()
        let recaptchaScripta = document.createElement('script')
        recaptchaScripta.setAttribute('type',"text/javascript")
        recaptchaScripta.setAttribute('src', "./theme/js/script.js")
        document.body.appendChild(recaptchaScripta)
        let recaptchaScript = document.createElement('script')
        recaptchaScript.setAttribute('type',"text/javascript")
        recaptchaScript.setAttribute('src', "./theme/js/swiper.js")
        document.body.appendChild(recaptchaScript)
        // this.items.content_val = this.items.content_val.replace("<br />","\n");
        for(var a=0; a<this.itrlist.length; a++){
            var colorCode  = "#" + Math.round(Math.random() * 0xffffff).toString(16);
            var div = document.createElement('div');
            var span = document.createElement('span');
            var bold = document.createElement('bold')
            var text = this.itrlist[a];
            if(text==""){
              return;
            }
            // this.itrlist.push(text);
            bold.innerText = text;
            div.style.background=colorCode;
            div.classList.add('roundedge');
            div.classList.add('itrlone');
            div.classList.add('text-white');
            span.classList.add('icon-remove')
            span.addEventListener("click", function(event){
              event.target.parentNode.remove();
            });
            div.appendChild(bold);
            div.appendChild(span);
            document.getElementById('itrl').appendChild(div);
        }

    }
}
</script>

<style>
.normal img{
  width:100%;
  z-index:1;
}
  body{
    padding-right:0px;
  }
</style>