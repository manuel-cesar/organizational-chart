<template>
  <div>
    <organization-chart :datasource="agencyRecords">
      <template v-slot:default="{ nodeData }">
        <div :class="['org-node', nodeData.title === 'AGENCY' ? 'agency-node' : '', nodeData.title]">
          <img v-if="nodeData.image" :src="nodeData.image" class="avatar">
          <div class="details">
            <h2 :class="titleColors[nodeData.title]">{{ roleTitles[nodeData.title] || nodeData.title }}</h2>
            <h4>{{ nodeData.name }}</h4>
          </div>
        </div>
      </template>
    </organization-chart>
  </div>
</template>

<script>
  import OrganizationChart from 'vue-organization-chart'
  import 'vue-organization-chart/dist/orgchart.css'
  import records from '@/assets/records.json';
  import { roleTitles, titleColors } from '../utils/utils.js'
  export default {
    components: {
      OrganizationChart
    },
    data () {
      return {
        records,
        agencyRecords: [],
        roleTitles,
        titleColors,
      };
    },
    async created () {
      this.agencyRecords = this.createChart(this.records);
      
    },
    methods: {
      createChart (records) {
          const agency = {
          id: 'agency',
          name: 'Clients',
          title: 'AGENCY',
          children: []
      };
      const nodesMap = { agency }; 
      records.forEach(record => {
          let node;

          if (record.type.name === 'CLIENT') {
              node = {
                  id: record.id,
                  name: record.name,
                  gender: record.gender,
                  image: record.image,
                  title: record.type.name,
                  children: [] 
              };
              agency.children.push(node);
          }

          if (Array.isArray(record.accesses)) {
              record.accesses.forEach(access => {
                  const userNode = {
                      id: access.user.id,
                      name: access.user.name,
                      image: access.user.image,
                      title: access.role.name,
                      children: []
                  };
                  nodesMap[access.user.id] = userNode;
                  
                  if (node) { 
                      node.children.push(userNode);
                  }
              });
          }
      });

      return agency;
      }
    }
  }
</script>

<style>

  .org-node:hover  {
      background-color: #dcedf7;
      border-color: #0068ad;
  }

  .org-node .avatar {
    width: 100px;
    height: 100px;
    margin-bottom: 5px;
    border-radius: 50%;
    padding: auto;
    border: 3px solid #b4b9d1;
  }

  .details {
    margin-top: -10px;
    text-align: center;
    background-color: #fff;
    border: 1px solid #b4b9d1;
    border-radius: 5px;
  }

  .lines {
    border: 1px solid black;
  }


  .details >h2 {
    border-radius: 5px;
    color: #fff;
    padding: 2px 0px;
    margin-bottom: 2px;
  }

  .details >p {
      border-radius: 5px;
      padding: 2px 0px;
      margin-bottom: 5px;
    }

  .orgchart {
    margin-top: 50px;
    background: #f4fbff;
    border-radius: 10px;
    padding: 50px;
  }

  .orgchart .node:hover {
      background-color: #f4fbff;
      border-radius: 10px;
    }

  .orgchart-container {
    position: relative;
    display: inline-block;
    height: 100vh; 
    width: 100vw; 
    border-radius: 5px;
    overflow: auto;
    text-align: center;
  }

  .orgchart .lines:nth-child(3) td {
      height: 80px;
    }

  .orgchart .lines .rightLine {
    border-right: 1px solid #0068ad;
    float: none;
    border-radius: 0;
  }

  .orgchart .lines .leftLine {
    border-left: 1px solid #0068ad;
    float: none;
    border-radius: 0;
  }

  .orgchart .lines .topLine {
    border-top: 2px solid #0068ad;
  }

  .orgchart .lines .downLine {
    background-color: #0068ad;
    height: 30px;
    width: 3px;
    float: none;
  }

  .level-1-title {
      background-color: green;
  }

  .level-2-title {
      background-color: blue;
  }

  .admin-title {
      background-color: red;
  }

  .client-title {
      background-color: gray;
  }

  .agency-title {
      background-color: blueviolet;
  }

  .orgchart .node {
    width: 250px;
  }

  .org-node.PROVIDER_LEVEL_1 {
    margin-top: 10em;
  }

  .org-node.PROVIDER_LEVEL_ADMIN {
    margin-top: 20em;
  }

</style>
