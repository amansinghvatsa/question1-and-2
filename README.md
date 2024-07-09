# question1-and-2
const express = require('express');
const router = express.Router();
const { MongoClient } = require('mongodb');

const uri = 'your-mongodb-connection-string';
const client = new MongoClient(uri, { useNewUrlParser: true, useUnifiedTopology: true });

router.get('/products/:minPrice', async (req, res) => {
    const minPrice = parseFloat(req.params.minPrice);
    try {
        await client.connect();
        const database = client.db('your-database-name');
        const products = database.collection('products');
        const query = { price: { $gt: minPrice } };
        const options = {
            sort: { price: -1 }
        };
        const result = await products.find(query, options).toArray();
        res.status(200).json(result);
    } catch (err) {
        res.status(500).json({ error: err.message });
    } finally {
        await client.close();
    }
});

module.exports = router;
