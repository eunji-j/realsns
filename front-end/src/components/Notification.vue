<template>
   <div class="container-fluid photos">
      <div class="row justify-content-center">
        <div class="col-md-8 pt-4">
            <div class="mb-5" data-aos="fade-up">
              <div class="row mb-5">
                  <div class="col-12">
                    <h2 class="text-white mb-4">Notification</h2>
                  </div>
              </div>
              <!-- <h2 class="mb-5 text-center text-light">읽지 않은 알림</h2> -->
              <div class="text-right" v-if="allNotilength>0"><button class="btn btn-outline-success" data-toggle="modal" data-target="#allReadModal">모두 읽음</button></div>
              <nav class="navbar navbar-expand-lg pb-0" style="background-color: black;">
                <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
                  <i class="icon-menu" style="font-size: 1.5em;"></i>
                </button>
                <div class="collapse navbar-collapse" id="navbarNav">
                  <ul class="navbar-nav nav nav-tabs row" id="myTab" role="tablist">
                    <li class="nav-item col-lg px-1">
                      <a class="nav-link m-0 px-0 text-center" :class="{'active': check=='all'}" @click="check='all'">전체 <span class="badge badge-primary">{{allNotilength}}</span></a>
                    </li>
                    <li class="nav-item col-lg px-1">
                      <a class="nav-link m-0 px-0 text-center" :class="{'active': check=='follow'}" @click="check='follow'">팔로우 <span class="badge badge-dark">{{followNoti}}</span></a>
                    </li>
                    <li class="nav-item col-lg px-1">
                      <a class="nav-link m-0 px-0 text-center" :class="{'active': check=='like'}" @click="check='like'">좋아요 <span class="badge badge-dark">{{likeNoti}}</span></a>
                    </li>
                    <li class="nav-item col-lg px-1">
                      <a class="nav-link m-0 px-0 text-center" :class="{'active': check=='scrap'}" @click="check='scrap'">스크랩 <span class="badge badge-dark">{{scrapNoti}}</span></a>
                    </li>
                    <li class="nav-item col-lg px-1">
                      <a class="nav-link m-0 px-0 text-center" :class="{'active': check=='report'}" @click="check='report'">신고 <span class="badge badge-dark">{{reportNoti}}</span></a>
                    </li>
                  </ul>
                </div>
              </nav>
            
              <table class="table text-light">
                <tbody v-if="check=='all'">
                  <tr v-for="(item, index) in allNoti" :key="`follow${index}`" :class="{read: item.is_checked}" @click="read(item)">
                    <td v-if="item.category == 'follow'" class="row py-0 mx-0"  >
                      <div class="col-md-10 d-flex">
                        <img class="rounded-circle" width="50px" height="50px" style="object-fit: cover;" :src="item.profile_url">
                        <div class="ml-2">
                          <div class="text-left">
                            <router-link class="m-0" :to="'/mypage/'+item.user_id">
                              <span style="font-weight: 550;">{{item.user_id}} </span>
                            </router-link>님이 회원님을 팔로우하기 시작했습니다.
                          </div>
                          <div class="text-left ml-1"><small>{{require('moment')(item.timestamp, "YYYY-MM-DD HH:mm:ss").fromNow()}}</small></div>
                        </div>
                      </div>
                      <div class="col-md-2 d-flex align-items-center justify-content-center">
                        <div v-if="myId != item.user_id && myFollowList.includes(item.user_id)" @click="modalToData(item.user_id)" data-toggle="modal" data-target="#exampleModal" class="btn btn-outline-primary">팔로잉</div>
                        <span v-if="myId != item.user_id && !myFollowList.includes(item.user_id)" @click="insertFollow(item.user_id)" class="btn btn-primary">팔로우</span>
                      </div>
                    </td>
                    <td v-if="item.category == 'like'" class="row py-0 mx-0">
                      <div class="col-md-10 d-flex">
                        <img class="rounded-circle" width="50px" height="50px" style="object-fit: cover;" :src="item.profile_url">
                        <div class="ml-2">
                          <div class="text-left">
                            <router-link class="m-0" :to="'/mypage/'+item.user_id"><span style="font-weight: 550;">
                              {{item.user_id}} </span>
                            </router-link>님이 회원님의 게시물을 좋아합니다.
                          </div>
                          <div class="text-left ml-1"><small>{{require('moment')(item.timestamp, "YYYY-MM-DD HH:mm:ss").fromNow()}}</small></div>
                        </div>
                      </div>
                      <div class="col-md-2 d-flex align-items-center justify-content-center">
                        <a class="m-0 p-0" href="javascript:void(0)" @click="$router.push({name: 'bio', params: {cid: item.content.content_id}})">
                          <img class="mb-2" width="70px" height="70px" style="object-fit: cover;" :src="item.content.imageList[0].image_url">
                        </a>
                      </div>
                    </td>
                    <td v-if="item.category == 'scrap'" class="row py-0 mx-0">
                      <div class="col-md-10 d-flex">
                        <img class="rounded-circle" width="50px" height="50px" style="object-fit: cover;" :src="item.profile_url">
                        <div class="ml-2">
                          <div class="text-left">
                            <router-link class="m-0" :to="'/mypage/'+item.user_id"><span style="font-weight: 550;">
                              {{item.user_id}} </span>
                            </router-link>님이 회원님의 게시물을 스크랩했습니다.
                          </div>
                          <div class="text-left ml-1"><small>{{require('moment')(item.timestamp, "YYYY-MM-DD HH:mm:ss").fromNow()}}</small></div>
                        </div>
                      </div>
                      <div class="col-md-2">
                        <a class="m-0 p-0" href="javascript:void(0)" @click="$router.push({name: 'bio', params: {cid: item.content.content_id}})">
                          <img class="mb-2" width="70px" height="70px" style="object-fit: cover;" :src="item.content.imageList[0].image_url">
                        </a>
                      </div>
                    </td>
                    <td v-if="item.category == 'report'" class="row py-0 mx-0">
                      <div class="col-md-10 d-flex">
                        <img class="rounded-circle" width="50px" height="50px" style="object-fit: cover;" :src="item.profile_url">
                        <div class="ml-2">
                          <div class="text-left">
                            <router-link class="m-0" :to="'/mypage/'+item.user_id"><span style="font-weight: 550;">
                              {{item.user_id}} </span>
                            </router-link>님이 회원님의 게시물을 신고했습니다.
                          </div>
                          <div class="text-left ml-1"><small>{{require('moment')(item.timestamp, "YYYY-MM-DD HH:mm:ss").fromNow()}}</small></div>
                        </div>
                      </div>
                      <div class="col-md-2">
                        <a class="m-0 p-0" href="javascript:void(0)" @click="$router.push({name: 'bio', params: {cid: item.content.content_id}})">                       
                          <img class="mb-2" width="70px" height="70px" style="object-fit: cover;" :src="item.content.imageList[0].image_url">
                        </a>
                      </div>
                    </td> 
                  </tr>
                </tbody>
                <tbody v-if="check=='follow'">
                  <tr v-for="(item, index) in allNoti" :key="`follow${index}`" :class="{read: item.is_checked}" @click="read(item)">
                    <td v-if="item.category == 'follow'" class="row py-0 mx-0">
                      <div class="col-md-10 d-flex">
                        <img class="rounded-circle" width="50px" height="50px" style="object-fit: cover;" :src="item.profile_url">
                        <div class="ml-2">
                          <div class="text-left">
                            <router-link class="m-0" :to="'/mypage/'+item.user_id"><span style="font-weight: 550;">
                              {{item.user_id}} </span>
                            </router-link>님이 회원님을 팔로우하기 시작했습니다.
                          </div>
                          <div class="text-left ml-1"><small>{{require('moment')(item.timestamp, "YYYY-MM-DD HH:mm:ss").fromNow()}}</small></div>
                        </div>
                      </div>
                      <div class="col-md-2 d-flex align-items-center justify-content-center">
                        <!-- <span v-if="myId != item.user_id && myFollowList.includes(item.user_id)" @click="deleteFollow(item.user_id)"  class="btn btn-outline-primary">팔로잉</span> -->
                        <div v-if="myId != item.user_id && myFollowList.includes(item.user_id)" @click="modalToData(item.user_id)" data-toggle="modal" data-target="#exampleModal" class="btn btn-outline-primary">팔로잉</div>
                        <span v-if="myId != item.user_id && !myFollowList.includes(item.user_id)" @click="insertFollow(item.user_id)" class="btn btn-primary">팔로우</span>
                      </div>
                    </td>
                  </tr>
                </tbody>
                <tbody v-if="check=='like'">
                  <tr v-for="(item, index) in allNoti" :key="`follow${index}`" :class="{read: item.is_checked}" @click="read(item)">
                    <td v-if="item.category == 'like'" class="row py-0 mx-0">
                      <div class="col-md-10 d-flex">
                        <img class="rounded-circle" width="50px" height="50px" style="object-fit: cover;" :src="item.profile_url">
                        <div class="ml-2">
                          <div class="text-left">
                            <router-link class="m-0" :to="'/mypage/'+item.user_id"><span style="font-weight: 550;">
                              {{item.user_id}} </span>
                            </router-link>님이 회원님의 게시물을 좋아합니다.
                          </div>
                          <div class="text-left ml-1"><small>{{require('moment')(item.timestamp, "YYYY-MM-DD HH:mm:ss").fromNow()}}</small></div>
                        </div>
                      </div>
                      <div class="col-md-2">
                        <a class="m-0 p-0" href="javascript:void(0)" @click="$router.push({name: 'bio', params: {cid: item.content.content_id}})">
                          <img class="mb-2" width="70px" height="70px" style="object-fit: cover;" :src="item.content.imageList[0].image_url">
                        </a>
                      </div>
                    </td>
                  </tr>
                </tbody>
                <tbody v-if="check=='scrap'">
                  <tr v-for="(item, index) in allNoti" :key="`follow${index}`" :class="{read: item.is_checked}" @click="read(item)">
                    <td v-if="item.category == 'scrap'" class="row py-0 mx-0">
                      <div class="col-md-10 d-flex">
                        <img class="rounded-circle" width="50px" height="50px" style="object-fit: cover;" :src="item.profile_url">
                        <div class="ml-2">
                          <div class="text-left">
                            <router-link class="m-0" :to="'/mypage/'+item.user_id"><span style="font-weight: 550;">
                              {{item.user_id}} </span>
                            </router-link>님이 회원님의 게시물을 스크랩했습니다.
                          </div>
                          <div class="text-left ml-1"><small>{{require('moment')(item.timestamp, "YYYY-MM-DD HH:mm:ss").fromNow()}}</small></div>
                        </div>
                      </div>
                      <div class="col-md-2">
                        <a class="m-0 p-0" href="javascript:void(0)" @click="$router.push({name: 'bio', params: {cid: item.content.content_id}})">
                          <img class="mb-2" width="70px" height="70px" style="object-fit: cover;" :src="item.content.imageList[0].image_url">
                        </a>
                      </div>
                    </td>
                  </tr>
                </tbody>
                <tbody v-if="check=='report'">
                  <tr v-for="(item, index) in allNoti" :key="`follow${index}`" :class="{read: item.is_checked}" @click="read(item)">
                    <td v-if="item.category == 'report'" class="row py-0 mx-0">
                      <div class="col-md-10 d-flex">
                        <img class="rounded-circle" width="50px" height="50px" style="object-fit: cover;" :src="item.profile_url">
                        <div class="ml-2">
                          <div class="text-left">
                            <router-link class="m-0" :to="'/mypage/'+item.user_id"><span style="font-weight: 550;">
                              {{item.user_id}} </span>
                            </router-link>님이 회원님의 게시물을 신고했습니다.
                          </div>
                          <div class="text-left ml-1"><small>{{require('moment')(item.timestamp, "YYYY-MM-DD HH:mm:ss").fromNow()}}</small></div>
                        </div>
                      </div>
                      <div class="col-md-2">
                        <a class="m-0 p-0" href="javascript:void(0)" @click="$router.push({name: 'bio', params: {cid: item.content.content_id}})">
                          <img class="mb-2" width="70px" height="70px" style="object-fit: cover;" :src="item.content.imageList[0].image_url">
                        </a>
                      </div>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
        </div>
      <!-- Modal -->
      <div class="modal fade mt-5" id="exampleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true" style="z-index: 99999;">
        <div class="modal-dialog" role="document">
          <div class="modal-content">
            <div class="modal-body py-4">
              <p class="m-2"> 팔로우를 취소하시겠습니까? </p>
            </div>
            <div class="modal-footer d-flex justify-content-end py-2">
              <div class="d-block">
                <button type="button" class="btn btn-danger mr-2 py-1 px-3" data-dismiss="modal" @click="deleteFollow()">확인</button>
                <button type="button" class="btn btn-secondary py-1 px-3" data-dismiss="modal">취소</button>
              </div>
            </div>
          </div>
        </div>
      </div>



      <!-- 모두읽음 모달 -->
      <div class="modal fade mt-5" id="allReadModal" tabindex="-1" role="dialog" style="z-index:99999;">
          <div class="modal-dialog" role="document">
              <div class="modal-content">
                  <div class="modal-body py-4 text-center">
                    알림을 모두 삭제하겠습니까?
                  </div>
                  <div class="modal-footer py-2">
                      <button type="button" class="btn btn-danger btn-sm py-1 px-3" @click="allRead" data-dismiss="modal">확인</button>
                      <button type="button" class="btn btn-secondary btn-sm py-1 px-3" data-dismiss="modal">취소</button>
                  </div>
              </div>
          </div>
      </div>

      </div>
    </div>

</template>

<script>
import http from '../http-common.js';
require('moment/locale/ko');

export default {
  data() {
      return {
        allNoti: [],
        allNotilength: 0,
        followNoti: 0,
        likeNoti: 0,
        scrapNoti: 0,
        reportNoti: 0,
        check: 'all',
        myId: this.$store.state.user_id,
        myFollowList: [],
        targetUser: "",
      }
    },
    methods: {
      modalToData(id) {
        this.targetUser = ""
        this.targetUser = id
      },
      fetchNoti() {
        http
          .get(`/notification/uncheckedList/${this.$store.state.user_id}`)
          .then(response => {
              this.allNoti = response.data.resvalue;
              this.allNoti.reverse();
              this.allNoti = this.allNoti;
              this.allNotilength = this.allNoti.length;
              this.allNoti.forEach(item => {
                if (item.category == 'follow') this.followNoti += 1
                else if (item.category == 'like') this.likeNoti += 1
                else if (item.category == 'scrap') this.scrapNoti += 1
                else if (item.category == 'report') this.reportNoti += 1
              })
              // console.log(this.allNoti);
          })
          .catch(e => console.log(e))
      },
      // fetchCategoryNoti(category) {
      //   http
      //     .get(`/notification/categoryUncheckedList/${this.$store.state.user_id}/${category}`)
      //     .then(response => {
      //         if (category == 'follow') {
      //           this.followNoti = response.data.resvalue;
      //           this.followNoti.reverse();
      //         }else if (category == 'like') {
      //           this.likeNoti = response.data.resvalue;
      //           this.likeNoti.reverse();
      //         }else if (category == 'scrap') {
      //           this.scrapNoti = response.data.resvalue;
      //           this.scrapNoti.reverse();
      //         }else if (category == 'report') {
      //           this.reportNoti = response.data.resvalue;
      //           this.reportNoti.reverse();
      //         }
      //     })
      //     .catch(e => console.log(e))
      // },
      insertFollow(id) {
        // this.$store.dispatch('INSERT_FOLLOW', {follower_id: this.myId, follow_id: id});
        http
          .post("/follow/insertFollow", {follower_id: this.myId, follow_id: id})
          .then(response => {
              this.$socket.emit('notification', {
                  user_id: response.data.resValue.user_id,
                  target_user_id: response.data.resValue.target_user_id,
                  category: response.data.resValue.category,
                  flag: true
              });
          })
          .catch(e => console.log(e))
        this.myFollowList.push(id);

        if (this.myId == this.userId) {
          this.fetchedFollowList.push(id);
        }
        else if (id == this.userId) {
          this.fetchedFollowerList.push(this.myId);
        }
      },
      deleteFollow() {
        const id = this.targetUser
        // if (confirm('팔로우취소 하시겠습니까?')) {
        //   http
        //         .delete("/follow/deleteFollow", {data: {follower_id: this.myId, follow_id: id}})
        //         .then(response => {
        //             this.$socket.emit('notification', {
        //                 user_id: response.data.resValue.user_id,
        //                 target_user_id: response.data.resValue.target_user_id,
        //                 category: response.data.resValue.category,
        //                 flag: false
        //             });
        //         })
        //         .catch(e => console.log(e))
        //   const idx = this.myFollowList.indexOf(id);
        //   if (idx > -1) this.myFollowList.splice(idx, 1);

        //   if (this.myId == this.userId) {
        //     const idx2 = this.fetchedFollowList.indexOf(id);
        //     if (idx2 > -1) this.fetchedFollowList.splice(idx2, 1);
        //   }
        //   else if (id == this.userId) {
        //     const idx2 = this.fetchedFollowerList.indexOf(this.myId);
        //     if (idx > -1) this.fetchedFollowerList.splice(idx2, 1);
        //   }
        // }
          http
            .delete("/follow/deleteFollow", {data: {follower_id: this.myId, follow_id: id}})
            .then(response => {
                this.$socket.emit('notification', {
                    user_id: response.data.resValue.user_id,
                    target_user_id: response.data.resValue.target_user_id,
                    category: response.data.resValue.category,
                    flag: false
                });
            })
            .catch(e => console.log(e))
          const idx = this.myFollowList.indexOf(id);
          if (idx > -1) this.myFollowList.splice(idx, 1);

          if (this.myId == this.userId) {
            const idx2 = this.fetchedFollowList.indexOf(id);
            if (idx2 > -1) this.fetchedFollowList.splice(idx2, 1);
          }
          else if (id == this.userId) {
            const idx2 = this.fetchedFollowerList.indexOf(this.myId);
            if (idx > -1) this.fetchedFollowerList.splice(idx2, 1);
          }

      },
      fetchMyFollowList() {
        http
          .get(`/follow/followList/${this.myId}`)
          .then(response => {
              response.data.resvalue.forEach(user => {
                        this.myFollowList.push(user.user_id)
                    })
          })
          .catch(e => console.log(e))
      },
      allRead() {
        for (let i in this.allNoti) {
          http
            .put(`notification/updateCheck/${this.allNoti[i].notification_id}`)
            .then(response => {
              this.$store.dispatch('FETCH_NOTI', this.myId);
              return response
            })
            .catch(e => console.log(e))
        }
        this.allNoti = [];
        this.allNotilength = 0;
        this.followNoti = 0;
        this.likeNoti = 0;
        this.scrapNoti = 0;
        this.reportNoti = 0;
      },
      read(item) {
        http
          .put(`notification/updateCheck/${item.notification_id}`)
          .then(response => {
            // console.log(response.data)
            this.$store.dispatch('FETCH_NOTI', this.myId);
            return response
          })
          .catch(e => console.log(e))
        
        if (item.is_checked == false) {
          this.allNotilength -= 1;
          if (item.category == 'follow') {
            this.followNoti -= 1;
          } else if (item.category == 'like') {
            this.likeNoti -= 1;
          } else if (item.category == 'scrap') {
            this.scrapNoti -= 1;
          } else if (item.category == 'report') {
            this.reportNoti -= 1;
          }
          item.is_checked = true;
        }
      } 
  },
  created() {
    this.fetchNoti();
    // this.fetchCategoryNoti('follow');
    // this.fetchCategoryNoti('like');
    // this.fetchCategoryNoti('scrap');
    // this.fetchCategoryNoti('report');
    this.fetchMyFollowList();
  },
}
</script>

<style scoped>
small {
  color: gray;
}
.read {
  opacity: 0.5;
}tr:hover{
  background-color: rgba(49, 49, 49, 0.747);
}
.nav-link {
  cursor: pointer;
}
  body{
    padding-right:0px;
  }
</style>