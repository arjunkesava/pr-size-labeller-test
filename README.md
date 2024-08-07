# pr-size-labeller-test
This repo is to build an git action to add label based on PR size

patch 7

#!/usr/bin/env node
const rp = require('request-promise');

function toCustomResponsePayload(body, response) {
  return {
    body: !/application\/json/i.test(response.headers['content-type']) ? body : JSON.parse(body),
  };
}

const apiUrlPrefix = 'https://api.github.com/repos/arjunkesava/pr-size-labeller';

const defaultOptions = {
  method: 'GET',
  headers: {
    Accept: 'application/vnd.github.v3+json',
    Authorization: `token eb7d938bad0240f3d9f62f95af7439538ac6ee60`,
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0',
  },
  transform: toCustomResponsePayload,
};

const standardLabels = ['size-s', 'size-m', 'size-xs'];

// Get Additions / Deletions count
rp({
  ...defaultOptions,
  uri: `${apiUrlPrefix}/pulls/2`,
}).then(prResponse => {
  console.log({prResponse});
  const { additions, deletions, labels } = prResponse.body;
  const totalSize = additions + deletions;
  let isNewLabelExists = false;
  let newLabel = '';

  if (totalSize < 10) {
    newLabel = "size-s";
  } else if (totalSize > 10 && totalSize < 25) {
    newLabel = "size-m";
  } else if (totalSize > 25) {
    newLabel = "size-xs";
  }


  // Check whether a label exists already
  if (labels.length) {
    for(const existingLabel of labels) {
      if (existingLabel.name === newLabel) {
        isNewLabelExists = true;
      } else if(existingLabel.name !== newLabel && standardLabels.some(label => label === existingLabel.name)) {
        rp({
          ...defaultOptions,
          method: 'DELETE',
          url: `${apiUrlPrefix}/issues/2/labels/${existingLabel.name}`
        }).then(res => {
          console.log(`${existingLabel.name} Label Deleted!!!`);
          console.log(res);
        });
        break;
      }
    }
  }

  const labelBody = {
    labels: [newLabel],
  };

  if(!isNewLabelExists) {
    // POST the label to that PR
    rp({
      ...defaultOptions,
      method: 'POST',
      uri: `${apiUrlPrefix}/issues/2/labels`,
      body: JSON.stringify(labelBody),
    }).then(res => {
      console.log(res);
    });
  }
})


  #!/usr/bin/env node
const rp = require('request-promise');

function toCustomResponsePayload(body, response) {
  return {
    body: !/application\/json/i.test(response.headers['content-type']) ? body : JSON.parse(body),
  };
}

const apiUrlPrefix = 'https://api.github.com/repos/arjunkesava/pr-size-labeller';

const defaultOptions = {
  method: 'GET',
  headers: {
    Accept: 'application/vnd.github.v3+json',
    Authorization: `token eb7d938bad0240f3d9f62f95af7439538ac6ee60`,
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0',
  },
  transform: toCustomResponsePayload,
};

const standardLabels = ['size-s', 'size-m', 'size-xs'];

// Get Additions / Deletions count
rp({
  ...defaultOptions,
  uri: `${apiUrlPrefix}/pulls/2`,
}).then(prResponse => {
  console.log({prResponse});
  const { additions, deletions, labels } = prResponse.body;
  const totalSize = additions + deletions;
  let isNewLabelExists = false;
  let newLabel = '';

  if (totalSize < 10) {
    newLabel = "size-s";
  } else if (totalSize > 10 && totalSize < 25) {
    newLabel = "size-m";
  } else if (totalSize > 25) {
    newLabel = "size-xs";
  }


  // Check whether a label exists already
  if (labels.length) {
    for(const existingLabel of labels) {
      if (existingLabel.name === newLabel) {
        isNewLabelExists = true;
      } else if(existingLabel.name !== newLabel && standardLabels.some(label => label === existingLabel.name)) {
        rp({
          ...defaultOptions,
          method: 'DELETE',
          url: `${apiUrlPrefix}/issues/2/labels/${existingLabel.name}`
        }).then(res => {
          console.log(`${existingLabel.name} Label Deleted!!!`);
          console.log(res);
        });
        break;
      }
    }
  }

  const labelBody = {
    labels: [newLabel],
  };

  if(!isNewLabelExists) {
    // POST the label to that PR
    rp({
      ...defaultOptions,
      method: 'POST',
      uri: `${apiUrlPrefix}/issues/2/labels`,
      body: JSON.stringify(labelBody),
    }).then(res => {
      console.log(res);
    });
  }
})


  
