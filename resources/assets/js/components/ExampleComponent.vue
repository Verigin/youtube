<template>
<div style="margin:20px">

<div class="row">
    <div class="col-md-4">
    <form>
        <div class="form-group">
            <label for="exampleInputEmail1">Username</label>
            <input type="email" class="form-control" id="exampleInputEmail1" aria-describedby="emailHelp" placeholder="Enter Username" v-model="name">
        </div>
        <button type="submit" class="btn btn-primary" v-on:click.prevent="loadChanels()">Submit</button>
    </form>
    <br/>
    </div>
</div>    

<div class="row">
      <div class="col-md-12">

<a href="#" class="btn btn-primary" v-if="prevPageToken!=''" v-on:click.prevent="loadItems(url+'&pageToken='+prevPageToken)">prev</a>
<a href="#" class="btn btn-primary" v-if="nextPageToken!=''" v-on:click.prevent="loadItems(url+'&pageToken='+nextPageToken)">next</a>
   <br/><br/>
    <table  class="table" cellspacing="0" v-if="seen">
	<thead>
		<tr>
			<th >Position</th>
			<th style="width:20%">Title</th>
			<th >description</th>
			<th >publishedAt</th>
		</tr>
	</thead>
	<tbody>
        <tr v-for="item in items" v-bind:key="item.id" class="cart_item bundle_table_item">
            <td>{{ item.snippet.position }}</td>
            <td>{{ item.snippet.title }}</td>
            <td>{{ item.snippet.description }}</td>
            <td>{{ item.snippet.publishedAt }}</td>
        </tr> 
	</tbody>
</table>

      </div>
      </div>

</div>
</template>

<script>
export default {
  mounted() {
    console.log("Component mounted.");
  },
  data() {
    return {
      name: "OneDirectionVEVO",
      items: [],
      seen: false,
      nextPageToken: "",
      prevPageToken: "",
      url: ""
    };
  },
  methods: {
    loadChanels() {
      this.seen = false;
      this.items = [];
      console.log("load chanels", this.name);
      var _this = this;
      this.$http
        .get(
          "https://www.googleapis.com/youtube/v3/channels?part=contentDetails&forUsername=" +
            _this.name +
            "&key=AIzaSyDhcTUsC46ZMfFR9Zxdyoi_jLUFq6ikWbY"
        )
        .then(
          function(success) {
            console.log(success.body.items);
            for (var i = 0; i <= success.body.items.length; i++) {
              _this.url =
                "https://www.googleapis.com/youtube/v3/playlistItems?part=snippet&playlistId=" +
                success.body.items[i].contentDetails.relatedPlaylists.uploads +
                "&key=AIzaSyDhcTUsC46ZMfFR9Zxdyoi_jLUFq6ikWbY&maxResults=5";
              _this.loadItems(_this.url);
            }
          },
          function(error) {
            console.log(error);
          }
        );
    },
    loadItems(url) {
      var _this = this;
      console.log("load items", url);
      this.$http.get(url).then(
        function(success) {
          this.seen = true;
          console.log(success.body.items);
          _this.items = success.body.items;
          _this.nextPageToken = success.body.nextPageToken;
          _this.prevPageToken = success.body.prevPageToken;
          console.log(_this.nextPageToken);
        },
        function(error) {
          console.log(error);
        }
      );
    }
  }
};
</script>
