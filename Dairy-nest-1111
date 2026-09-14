import React, { useState, useMemo, useEffect, useCallback } from 'react';
import {
  SafeAreaView,
  View,
  Text,
  FlatList,
  TouchableOpacity,
  TextInput,
  StyleSheet,
  StatusBar,
  Alert,
  ActivityIndicator,
  ScrollView,
  Linking,
  Image,
} from 'react-native';
import { Ionicons } from '@expo/vector-icons';
import AsyncStorage from '@react-native-async-storage/async-storage';

const getDirectDriveUrl = (shareLink) => {
  const match = shareLink.match(/\/d\/([a-zA-Z0-9_-]+)/);
  return match ? `https://lh3.googleusercontent.com/d/${match[1]}` : shareLink;
};

const FIREBASE_PROJECT_ID = 'dairy-nest-6b0e2';
const FLAT_DELIVERY_FEE = 25;
const FREE_DELIVERY_THRESHOLD = 200;
const ADMIN_PIN = '0707';
const DELIVERY_PIN = '1111';
const ADMIN_WHATSAPP_PHONE = '9931507392';
const UPI_ID = 'csurjeet-13@axl';
const UPI_PAYEE_NAME = 'Dairy Nest';

const DELIVERY_AREAS = [
  'Sahebganj',
  'Newaji Tola',
  'Municipality Chowk',
  'Salempur',
  'Bhagwan Bazar',
  'Mauna Chowk',
];

const INITIAL_DELIVERY_AGENTS = [
  { name: 'Rohan Kumar', phone: '9876543210' },
  { name: 'Amit Singh', phone: '9123456789' },
  { name: 'Vikas Gupta', phone: '9988776655' },
];

const FIRESTORE_BASE_URL =
  'https://firestore.googleapis.com/v1/projects/' +
  FIREBASE_PROJECT_ID +
  '/databases/(default)/documents/orders';
const STOCK_BASE_URL =
  'https://firestore.googleapis.com/v1/projects/' +
  FIREBASE_PROJECT_ID +
  '/databases/(default)/documents/stock';

const INITIAL_PRODUCTS = [
  { id: '1', name: 'Amul Organic Tea (250 g)', cat: 'Organic & Staples', price: 150, icon: 'leaf' },
  { id: '2', name: 'Amul Organic Masoor Dal 1 kg', cat: 'Organic & Staples', price: 165, icon: 'nutrition' },
  { id: '3', name: 'Amul Organic Toor Dal 500gm', cat: 'Organic & Staples', price: 125, icon: 'nutrition' },
  { id: '4', name: 'Amul Organic Peanuts (500 g)', cat: 'Organic & Staples', price: 120, icon: 'nutrition' },
  { id: '5', name: 'Amul Organic Whole Wheat Atta (5 kg)', cat: 'Organic & Staples', price: 240, icon: 'basket' },
  { id: '6', name: 'Amul Organic Sona Masoori Rice (1 kg)', cat: 'Organic & Staples', price: 320, icon: 'basket' },
  { id: '7', name: 'Amul Natural Honey (200 g Squeeze Bottle)', cat: 'Organic & Staples', price: 100, icon: 'flask' },
  { id: '8', name: 'Amul Organic Poha', cat: 'Organic & Staples', price: 60, icon: 'nutrition' },
  { id: '9', name: 'Amul 1Kg Brown Sugar', cat: 'Organic & Staples', price: 80, icon: 'basket' },
  { id: '10', name: 'Amul Gold Milk (500 ml Pouch)', cat: 'Fresh & Refrigerated Dairy', price: 35, icon: 'water' },
  { id: '11', name: 'Amul Gold Full Cream Milk (1 L Tetra Pack)', cat: 'Fresh & Refrigerated Dairy', price: 83, icon: 'water' },
  { id: '12', name: 'Amul Malai Paneer (200 g Pouch)', cat: 'Fresh & Refrigerated Dairy', price: 95, icon: 'square' },
  { id: '13', name: 'Amul Paneer 1Kg', cat: 'Fresh & Refrigerated Dairy', price: 435, icon: 'square' },
  { id: '14', name: 'Amul Masti Dahi Pouch', cat: 'Fresh & Refrigerated Dairy', price: 35, icon: 'ice-cream' },
  { id: '15', name: 'Amul Dahi Cup', cat: 'Fresh & Refrigerated Dairy', price: 25, icon: 'ice-cream' },
  { id: '16', name: 'Amul Meetha Dahi (Sweet Curd Cups)', cat: 'Fresh & Refrigerated Dairy', price: 22, icon: 'ice-cream' },
  { id: '17', name: 'Amul Fresh Cream 250ml', cat: 'Fresh & Refrigerated Dairy', price: 75, icon: 'flask' },
  { id: '18', name: 'Amul Fresh Cream 1 litre', cat: 'Fresh & Refrigerated Dairy', price: 250, icon: 'flask' },
  { id: '19', name: 'Amul Pasteurised Butter 200 gm', cat: 'Butter, Cheese & Spreads', price: 130, icon: 'square-outline' },
  { id: '20', name: 'Amul Pasteurised Butter 100 gm', cat: 'Butter, Cheese & Spreads', price: 63, icon: 'square-outline' },
  { id: '21', name: 'Amul Pasteurised Butter 500 gm', cat: 'Butter, Cheese & Spreads', price: 310, icon: 'square-outline' },
  { id: '22', name: 'Amul Processed Cheese Spread Spicy Garlic', cat: 'Butter, Cheese & Spreads', price: 115, icon: 'restaurant' },
  { id: '23', name: 'Amul Processed Cheese Spread Plain', cat: 'Butter, Cheese & Spreads', price: 115, icon: 'restaurant' },
  { id: '24', name: 'Amul Cream Cheese Spread', cat: 'Butter, Cheese & Spreads', price: 125, icon: 'restaurant' },
  { id: '25', name: 'Amul Cheese Slices 5 piece', cat: 'Butter, Cheese & Spreads', price: 82, icon: 'restaurant' },
  { id: '26', name: 'Amul Cheese Slices 10 pieces', cat: 'Butter, Cheese & Spreads', price: 145, icon: 'restaurant' },
  { id: '27', name: 'Amul Dice Cheese 200 gm', cat: 'Butter, Cheese & Spreads', price: 125, icon: 'restaurant' },
  { id: '28', name: 'Amul Pure Ghee Tetra 1L', cat: 'Ghee & Infant Formula', price: 660, icon: 'flask' },
  { id: '29', name: 'Amul Pure Ghee Tin 1L', cat: 'Ghee & Infant Formula', price: 670, icon: 'flask' },
  { id: '30', name: 'Amul Cow Ghee 1 L Tin', cat: 'Ghee & Infant Formula', price: 700, icon: 'flask' },
  { id: '31', name: 'Sagar Pure Ghee 1 kg Tin', cat: 'Ghee & Infant Formula', price: 670, icon: 'flask' },
  { id: '32', name: 'Amulspray Infant Milk Food (200 g Box)', cat: 'Ghee & Infant Formula', price: 113, icon: 'nutrition' },
  { id: '33', name: 'Amul Pure Ghee Plastic', cat: 'Ghee & Infant Formula', price: 670, icon: 'flask' },
  { id: '34', name: 'Amul Arabica Coffee', cat: 'Beverages & Drinks', price: 100, icon: 'cafe' },
  { id: '35', name: 'Amul Badam Shakers', cat: 'Beverages & Drinks', price: 40, icon: 'beer' },
  { id: '36', name: 'Amul Kool Glass', cat: 'Beverages & Drinks', price: 30, icon: 'beer' },
  { id: '37', name: 'Amul Milk Shake Double Chocolate', cat: 'Beverages & Drinks', price: 30, icon: 'beer' },
  { id: '38', name: 'Amul Milk Shake Double Chocolate Can', cat: 'Beverages & Drinks', price: 40, icon: 'beer' },
  { id: '39', name: 'Amul Kadhai Doodh', cat: 'Beverages & Drinks', price: 25, icon: 'beer' },
  { id: '40', name: 'Amul Haldi Doodh Can', cat: 'Beverages & Drinks', price: 25, icon: 'beer' },
  { id: '41', name: 'Amul Honey Doodh Can', cat: 'Beverages & Drinks', price: 25, icon: 'beer' },
  { id: '42', name: 'Amul Saffron Doodh Can', cat: 'Beverages & Drinks', price: 25, icon: 'beer' },
  { id: '43', name: 'Amul Lassi Tetra', cat: 'Beverages & Drinks', price: 20, icon: 'beer' },
  { id: '44', name: 'Amul Lassi Cap', cat: 'Beverages & Drinks', price: 25, icon: 'beer' },
  { id: '45', name: 'Amul Lassi Rose', cat: 'Beverages & Drinks', price: 25, icon: 'beer' },
  { id: '46', name: 'Amul Lassi Kesar', cat: 'Beverages & Drinks', price: 22, icon: 'beer' },
  { id: '47', name: 'Amul Buttermilk', cat: 'Beverages & Drinks', price: 15, icon: 'beer' },
  { id: '48', name: 'Amul 1 L Buttermilk', cat: 'Beverages & Drinks', price: 70, icon: 'beer' },
  { id: '49', name: 'Amul 1 L Lassi', cat: 'Beverages & Drinks', price: 90, icon: 'beer' },
  { id: '50', name: 'Amul Kesar Peda (200 g Box)', cat: 'Packaged Sweets & Mithai', price: 140, icon: 'gift' },
  { id: '51', name: 'Amul Malai Peda (200 g Box)', cat: 'Packaged Sweets & Mithai', price: 130, icon: 'gift' },
  { id: '52', name: 'Amul Milk Cake (200 g Box)', cat: 'Packaged Sweets & Mithai', price: 140, icon: 'gift' },
  { id: '53', name: 'Amul Besan Laddoo (200 g Box)', cat: 'Packaged Sweets & Mithai', price: 130, icon: 'gift' },
  { id: '54', name: 'Amul Kaju Katli (200 g Tray)', cat: 'Packaged Sweets & Mithai', price: 250, icon: 'gift' },
  { id: '55', name: 'Amul Gud (200 gm)', cat: 'Packaged Sweets & Mithai', price: 275, icon: 'gift' },
  { id: '56', name: 'Amul Gulab Jamun 1 kg Tin', cat: 'Packaged Sweets & Mithai', price: 270, icon: 'gift' },
  { id: '57', name: 'Amul Rasogolla 500gm Tin', cat: 'Packaged Sweets & Mithai', price: 125, icon: 'gift' },
  { id: '58', name: 'Amul Rasmalai Tubs (Per Cup)', cat: 'Packaged Sweets & Mithai', price: 55, icon: 'gift' },
  { id: '59', name: 'Amul Basundi Kesar Elaichi (250 ml Pack)', cat: 'Packaged Sweets & Mithai', price: 50, icon: 'gift' },
  { id: '60', name: "Amul Bakery Fruit 'N' Nut Cake", cat: 'Packaged Sweets & Mithai', price: 100, icon: 'gift' },
  { id: '61', name: 'Amul Dark Chocolate', cat: 'Chocolates & Snacks', price: 45, icon: 'nutrition' },
  { id: '62', name: "Amul Fruit 'N' Nut Chocolate", cat: 'Chocolates & Snacks', price: 45, icon: 'nutrition' },
  { id: '63', name: 'Amul Sugar Free Dark Chocolate', cat: 'Chocolates & Snacks', price: 45, icon: 'nutrition' },
  { id: '64', name: 'Amul Chocolate Syrup (Per Sachet)', cat: 'Chocolates & Snacks', price: 10, icon: 'nutrition' },
  { id: '65', name: 'Amul Specialty Chocolates Gift Box', cat: 'Chocolates & Snacks', price: 200, icon: 'gift' },
  { id: '66', name: 'Amul Almondo Box', cat: 'Chocolates & Snacks', price: 210, icon: 'gift' },
  { id: '67', name: 'Amul Truffles Box', cat: 'Chocolates & Snacks', price: 210, icon: 'gift' },
  { id: '68', name: 'Amul Chocominis', cat: 'Chocolates & Snacks', price: 140, icon: 'nutrition' },
  { id: '69', name: 'Amul Chocozoo', cat: 'Chocolates & Snacks', price: 130, icon: 'nutrition' },
  { id: '70', name: 'Amul Pro Energy Bar', cat: 'Chocolates & Snacks', price: 22, icon: 'flash' },
  { id: '71', name: 'Amul Almond Bar', cat: 'Chocolates & Snacks', price: 30, icon: 'flash' },
  { id: '72', name: 'Amul Cake Magic Black Forest Eggless Ice Cream Cake (500 ml)', cat: 'Ice Creams & Desserts', price: 350, icon: 'ice-cream' },
  { id: '73', name: 'Amul Frostik (Dark Chocolate Bar)', cat: 'Ice Creams & Desserts', price: 40, icon: 'ice-cream' },
  { id: '74', name: 'Frostik Gold Chocolate', cat: 'Ice Creams & Desserts', price: 45, icon: 'ice-cream' },
  { id: '75', name: 'Amul Pista Malai Kulfi', cat: 'Ice Creams & Desserts', price: 30, icon: 'ice-cream' },
  { id: '76', name: 'Rajbhog Kulfi Sticks', cat: 'Ice Creams & Desserts', price: 30, icon: 'ice-cream' },
  { id: '77', name: 'Amul Punjabi Kulfi', cat: 'Ice Creams & Desserts', price: 30, icon: 'ice-cream' },
  { id: '78', name: 'Amul Tru Pineapple', cat: 'Ice Creams & Desserts', price: 50, icon: 'ice-cream' },
  { id: '79', name: 'Amul Tender Coconut', cat: 'Ice Creams & Desserts', price: 40, icon: 'ice-cream' },
  { id: '80', name: 'Amul Sitaphal Bliss', cat: 'Ice Creams & Desserts', price: 60, icon: 'ice-cream' },
  { id: '81', name: 'Amul Tru Spicy Guava', cat: 'Ice Creams & Desserts', price: 50, icon: 'ice-cream' },
  { id: '82', name: 'Amul Butterscotch Gold', cat: 'Ice Creams & Desserts', price: 40, icon: 'ice-cream' },
  { id: '83', name: 'Amul Butterscotch Bliss', cat: 'Ice Creams & Desserts', price: 35, icon: 'ice-cream' },
  { id: '84', name: 'Amul Tricone Black Currant', cat: 'Ice Creams & Desserts', price: 30, icon: 'ice-cream' },
  { id: '85', name: 'Amul Tricone Butterscotch', cat: 'Ice Creams & Desserts', price: 40, icon: 'ice-cream' },
  { id: '86', name: 'Amul Belgian Chocolate', cat: 'Ice Creams & Desserts', price: 50, icon: 'ice-cream' },
  { id: '87', name: 'Amul Ice Cream Tubs 1L King Alphonso', cat: 'Ice Creams & Desserts', price: 260, icon: 'ice-cream' },
  { id: '88', name: 'Amul Ice Cream Tubs 1L Butterscotch', cat: 'Ice Creams & Desserts', price: 260, icon: 'ice-cream' },
  { id: '89', name: 'Amul Ice Cream Tubs 1L Sitaphal', cat: 'Ice Creams & Desserts', price: 385, icon: 'ice-cream' },
  { id: '90', name: 'Amul Ice Cream Tubs 1L Faaloda', cat: 'Ice Creams & Desserts', price: 300, icon: 'ice-cream' },
  { id: '91', name: 'Amul Ice Cream Tubs 1L Chocochip', cat: 'Ice Creams & Desserts', price: 230, icon: 'ice-cream' },
  { id: '92', name: 'Amul Golden Pearl Jumbo Cup', cat: 'Ice Creams & Desserts', price: 35, icon: 'ice-cream' },
  { id: '93', name: 'Amul Shalimar', cat: 'Ice Creams & Desserts', price: 35, icon: 'ice-cream' },
  { id: '94', name: "Amul Fruit 'N Nut", cat: 'Ice Creams & Desserts', price: 35, icon: 'ice-cream' },
  { id: '95', name: 'Amul Rajbhog Jumbo Cup', cat: 'Ice Creams & Desserts', price: 35, icon: 'ice-cream' },
  { id: '96', name: 'Amul King Alphonso Jumbo Cup', cat: 'Ice Creams & Desserts', price: 40, icon: 'ice-cream' },
  { id: '97', name: 'Amul Chocochip Jumbo Cup', cat: 'Ice Creams & Desserts', price: 50, icon: 'ice-cream' },
  { id: '98', name: 'Amul Kesar Kulfi', cat: 'Ice Creams & Desserts', price: 20, icon: 'ice-cream' },
  { id: '99', name: 'Amul Rajwadi Kulfi', cat: 'Ice Creams & Desserts', price: 10, icon: 'ice-cream' },
  { id: '100', name: 'Amul Brick Butterscotch', cat: 'Ice Creams & Desserts', price: 135, icon: 'ice-cream' },
  { id: '101', name: 'Amul Brick Chocolate', cat: 'Ice Creams & Desserts', price: 130, icon: 'ice-cream' },
  { id: '102', name: 'Amul Brick Vanilla', cat: 'Ice Creams & Desserts', price: 115, icon: 'ice-cream' },
  { id: '103', name: 'Amul Brick Strawberry', cat: 'Ice Creams & Desserts', price: 120, icon: 'ice-cream' },
  { id: '104', name: 'Amul Brick Tuti Fruti', cat: 'Ice Creams & Desserts', price: 130, icon: 'ice-cream' },
  { id: '105', name: 'Amul Brick Kesar Pista', cat: 'Ice Creams & Desserts', price: 195, icon: 'ice-cream' },
  { id: '106', name: 'India Twilight Tryst (Single Origin)', cat: 'Chocolate Bar', price: 200, icon: 'ribbon' },
  { id: '107', name: 'Velvett Milk Chocolate', cat: 'Chocolate Bar', price: 200, icon: 'ribbon' },
  { id: '108', name: '90% Bitter Dark Chocolate', cat: 'Chocolate Bar', price: 180, icon: 'ribbon' },
  { id: '109', name: 'Peru Dark Amazon (55% Cocoa)', cat: 'Chocolate Bar', price: 180, icon: 'ribbon' },
  { id: '110', name: 'Dark Chocolate (55% Cocoa)', cat: 'Chocolate Bar', price: 180, icon: 'ribbon' },
  { id: '111', name: "Fruit 'N' Nut Dark Chocolate", cat: 'Chocolate Bar', price: 180, icon: 'ribbon' },
  { id: '112', name: 'Crunchy Peanut Chocolate Bar', cat: 'Chocolate Bar', price: 180, icon: 'ribbon' },
  { id: '113', name: 'White Limón Chocolate', cat: 'Chocolate Bar', price: 170, icon: 'ribbon' },
  { id: '114', name: 'Sugar Free Dark Chocolate', cat: 'Chocolate Bar', price: 180, icon: 'ribbon' },
  { id: '115', name: 'Milk Chocolate', cat: 'Chocolate Bar', price: 180, icon: 'ribbon' },
  { id: '116', name: 'Milk Rusk', cat: 'Others', price: 45, icon: 'restaurant' },
  { id: '117', name: 'Elaichi Rusk', cat: 'Others', price: 40, icon: 'restaurant' },
  { id: '118', name: 'T-Special Rusk', cat: 'Others', price: 35, icon: 'restaurant' },
];

const CATEGORIES = ['All', ...new Set(INITIAL_PRODUCTS.map((p) => p.cat))];
function formatRupees(n) {
  return '\u20b9' + n.toLocaleString('en-IN');
}

function getField(fields, key, type) {
  if (!fields || !fields[key]) return '';
  return fields[key][type] !== undefined ? fields[key][type] : '';
}

export default function App() {
  const [isRegistered, setIsRegistered] = useState(false);
  const [userName, setUserName] = useState('');
  const [userPhone, setUserPhone] = useState('');
  const [userArea, setUserArea] = useState('');

  const [screen, setScreen] = useState('catalog');
  const [activeCat, setActiveCat] = useState('All');
  const [searchQuery, setSearchQuery] = useState('');
  const [cart, setCart] = useState({});

  const [checkoutName, setCheckoutName] = useState('');
  const [checkoutPhone, setCheckoutPhone] = useState('');
  const [checkoutArea, setCheckoutArea] = useState('');
  const [userAddress, setUserAddress] = useState('');

  const [paymentMethod, setPaymentMethod] = useState('COD');
  const [orderSubmitting, setOrderSubmitting] = useState(false);

  const [pinInput, setPinInput] = useState('');
  const [agentPhoneInput, setAgentPhoneInput] = useState('');
  const [activeAgentPhone, setActiveAgentPhone] = useState('');
  const [pinError, setPinError] = useState('');

  const [orders, setOrders] = useState([]);
  const [ordersLoading, setOrdersLoading] = useState(false);
  const [customDeliveryAgents, setCustomDeliveryAgents] = useState(INITIAL_DELIVERY_AGENTS);
  const [newAgentName, setNewAgentName] = useState('');
  const [newAgentPhone, setNewAgentPhone] = useState('');

  const [stockMap, setStockMap] = useState({});
  const [imageMap, setImageMap] = useState({});
  const [imageUrlInputs, setImageUrlInputs] = useState({});

  const [lookupPhoneInput, setLookupPhoneInput] = useState('');
  const [searchedPhone, setSearchedPhone] = useState('');

  useEffect(() => {
    const loadUserData = async () => {
      try {
        const savedName = await AsyncStorage.getItem('@dairy_user_name');
        const savedPhone = await AsyncStorage.getItem('@dairy_user_phone');
        const savedArea = await AsyncStorage.getItem('@dairy_user_area');
        if (savedName && savedPhone && savedArea) {
          setUserName(savedName);
          setUserPhone(savedPhone);
          setUserArea(savedArea);
          setIsRegistered(true);
        }
      } catch (e) {}
    };
    loadUserData();
  }, []);

  const loadStockAndImages = useCallback(async () => {
    try {
      const res = await fetch(STOCK_BASE_URL);
      const data = await res.json();
      const sMap = {};
      const iMap = {};
      (data.documents || []).forEach((doc) => {
        const id = doc.name.split('/').pop();
        sMap[id] = !!getField(doc.fields, 'outOfStock', 'booleanValue');
        const imgUri = getField(doc.fields, 'imageUri', 'stringValue');
        // Automatically run Google Drive URLs through the helper converter here:
        if (imgUri) iMap[id] = getDirectDriveUrl(imgUri);
      });
      setStockMap(sMap);
      setImageMap(iMap);
    } catch (e) {}
  }, []);

  useEffect(() => {
    loadStockAndImages();
  }, [loadStockAndImages]);

  const handleUpdateProductImage = async (productId) => {
    const url = imageUrlInputs[productId];
    if (!url || !url.trim()) {
      Alert.alert('Error', 'Please enter a valid image URL first.');
      return;
    }
    try {
      const payload = {
        fields: {
          imageUri: { stringValue: url.trim() },
        },
      };
      await fetch(STOCK_BASE_URL + '/' + productId, {
        method: 'PATCH',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(payload),
      });
      Alert.alert('Success', 'Product image updated successfully!');
      loadStockAndImages();
    } catch (error) {
      Alert.alert('Error', 'Failed to update image.');
    }
  };

  const loadOrders = useCallback(async () => {
    setOrdersLoading(true);
    try {
      const res = await fetch(FIRESTORE_BASE_URL);
      const data = await res.json();
      const docs = (data.documents || []).map((doc) => {
        const f = doc.fields;
        return {
          id: doc.name.split('/').pop(),
          customerName: getField(f, 'customerName', 'stringValue') || 'Guest',
          phone: getField(f, 'phone', 'stringValue'),
          address: getField(f, 'address', 'stringValue'),
          area: getField(f, 'area', 'stringValue'),
          items: getField(f, 'items', 'stringValue'),
          total: getField(f, 'total', 'integerValue') || '0',
          status: getField(f, 'status', 'stringValue') || 'New',
          deliveryBoyName: getField(f, 'deliveryBoyName', 'stringValue'),
          deliveryBoyPhone: getField(f, 'deliveryBoyPhone', 'stringValue'),
          createdAt: getField(f, 'createdAt', 'timestampValue'),
        };
      });
      docs.sort((a, b) => (a.createdAt < b.createdAt ? 1 : -1));
      setOrders(docs);
    } catch (e) {
      Alert.alert('Error', 'Unable to fetch orders.');
    } finally {
      setOrdersLoading(false);
    }
  }, []);

  const handleWhatsAppOrder = () => {
    const message = encodeURIComponent(
      `Hello Dairy Nest! I'd like to place a quick order or send a voice note.`
    );
    const url = `whatsapp://send?phone=${ADMIN_WHATSAPP_PHONE}&text=${message}`;
    Linking.canOpenURL(url)
      .then((supported) => {
        if (!supported) {
          Linking.openURL(`https://wa.me/${ADMIN_WHATSAPP_PHONE}?text=${message}`);
        } else {
          return Linking.openURL(url);
        }
      })
      .catch(() => Alert.alert('Error', 'Unable to open WhatsApp'));
  };

  const cartItemsList = useMemo(() => {
    return Object.keys(cart)
      .map((id) => {
        const prod = INITIAL_PRODUCTS.find((p) => p.id === id);
        const qty = cart[id];
        return { ...prod, qty, subtotal: prod.price * qty };
      })
      .filter((i) => i.qty > 0);
  }, [cart]);

  const cartTotalAmount = useMemo(() => {
    return cartItemsList.reduce((sum, item) => sum + item.subtotal, 0);
  }, [cartItemsList]);

  const deliveryFee = cartTotalAmount >= FREE_DELIVERY_THRESHOLD || cartTotalAmount === 0 ? 0 : FLAT_DELIVERY_FEE;
  const finalGrandTotal = cartTotalAmount + deliveryFee;

  if (!isRegistered) {
    return (
      <SafeAreaView style={styles.safeArea}>
        <StatusBar barStyle="dark-content" backgroundColor="#f5f5f5" />
        <View style={styles.regContainer}>
          <View style={styles.regCard}>
            <View style={styles.logoBadge}>
              <Ionicons name="basket" size={36} color="#0C447C" />
            </View>
            <Text style={styles.regTitle}>Welcome to Dairy Nest</Text>
            <Text style={styles.regSubtitle}>Chhapra's Ultimate Quick Delivery</Text>

            <TextInput
              style={styles.input}
              placeholder="Your Full Name"
              value={userName}
              onChangeText={setUserName}
            />
            <TextInput
              style={styles.input}
              placeholder="Phone Number (10 digits)"
              keyboardType="phone-pad"
              maxLength={10}
              value={userPhone}
              onChangeText={setUserPhone}
            />

            <Text style={styles.label}>Select Delivery Area:</Text>
            <ScrollView horizontal showsHorizontalScrollIndicator={false} style={{ marginBottom: 15 }}>
              {DELIVERY_AREAS.map((area) => (
                <TouchableOpacity
                  key={area}
                  style={[styles.areaChip, userArea === area && styles.areaChipActive]}
                  onPress={() => setUserArea(area)}>
                  <Text style={[styles.areaChipText, userArea === area && styles.areaChipTextActive]}>{area}</Text>
                </TouchableOpacity>
              ))}
            </ScrollView>

            <TouchableOpacity
              style={styles.primaryBtn}
              onPress={async () => {
                if (!userName.trim() || userPhone.trim().length < 10 || !userArea) {
                  Alert.alert('Incomplete', 'Please fill in your name, valid phone, and select an area.');
                  return;
                }
                try {
                  await AsyncStorage.setItem('@dairy_user_name', userName.trim());
                  await AsyncStorage.setItem('@dairy_user_phone', userPhone.trim());
                  await AsyncStorage.setItem('@dairy_user_area', userArea);
                } catch (e) {}
                setIsRegistered(true);
              }}>
              <Text style={styles.primaryBtnText}>Get Started</Text>
            </TouchableOpacity>

            <TouchableOpacity
              style={{ marginTop: 20, alignItems: 'center' }}
              onPress={() => setScreen('login')}>
              <Text style={{ color: '#0C447C', fontWeight: 'bold' }}>Staff / Delivery Portal Access</Text>
            </TouchableOpacity>
          </View>
        </View>
      </SafeAreaView>
    );
  }

  if (screen === 'login') {
    return (
      <SafeAreaView style={styles.safeArea}>
        <View style={styles.regContainer}>
          <View style={styles.regCard}>
            <Text style={styles.regTitle}>Portal Login</Text>
            <Text style={styles.regSubtitle}>Enter Admin PIN or Delivery PIN</Text>

            <TextInput
              style={styles.input}
              placeholder="Enter Portal PIN"
              secureTextEntry={true}
              value={pinInput}
              onChangeText={setPinInput}
              keyboardType="numeric"
            />

            {pinInput === DELIVERY_PIN && (
              <TextInput
                style={styles.input}
                placeholder="Enter Your Delivery Agent Phone Number"
                keyboardType="phone-pad"
                maxLength={10}
                value={agentPhoneInput}
                onChangeText={setAgentPhoneInput}
              />
            )}

            {pinError ? <Text style={styles.errorText}>{pinError}</Text> : null}

            <TouchableOpacity
              style={styles.primaryBtn}
              onPress={() => {
                if (pinInput === ADMIN_PIN) {
                  setPinError('');
                  setPinInput('');
                  setScreen('admin');
                  loadOrders();
                } else if (pinInput === DELIVERY_PIN) {
                  if (!agentPhoneInput.trim() || agentPhoneInput.trim().length < 10) {
                    setPinError('Please enter your valid 10-digit delivery phone number.');
                    return;
                  }
                  setActiveAgentPhone(agentPhoneInput.trim());
                  setPinError('');
                  setPinInput('');
                  setAgentPhoneInput('');
                  setScreen('deliveryPortal');
                  loadOrders();
                } else {
                  setPinError('Invalid PIN code.');
                }
              }}>
              <Text style={styles.primaryBtnText}>Authenticate</Text>
            </TouchableOpacity>

            <TouchableOpacity
              style={{ marginTop: 15, alignItems: 'center' }}
              onPress={() => setScreen('catalog')}>
              <Text style={{ color: '#666' }}>Return to Shop</Text>
            </TouchableOpacity>
          </View>
        </View>
      </SafeAreaView>
    );
  }

  if (screen === 'admin') {
    return (
      <SafeAreaView style={styles.safeArea}>
        <View style={styles.header}>
          <Text style={styles.headerTitle}>Admin Panel - Dairy Nest</Text>
          <TouchableOpacity onPress={() => setScreen('catalog')}>
            <Ionicons name="close-circle" size={28} color="#333" />
          </TouchableOpacity>
        </View>
        <ScrollView style={{ padding: 15 }}>
          <View style={styles.adminCard}>
            <Text style={{ fontSize: 16, fontWeight: 'bold', marginBottom: 10, color: '#333' }}>
              Add New Delivery Agent
            </Text>
            <TextInput
              style={styles.input}
              placeholder="Agent Name"
              value={newAgentName}
              onChangeText={setNewAgentName}
            />
            <TextInput
              style={styles.input}
              placeholder="Agent Phone Number"
              keyboardType="phone-pad"
              maxLength={10}
              value={newAgentPhone}
              onChangeText={setNewAgentPhone}
            />
            <TouchableOpacity
              style={styles.primaryBtn}
              onPress={() => {
                if (!newAgentName.trim() || newAgentPhone.trim().length < 10) {
                  Alert.alert('Error', 'Please enter agent name and a valid 10-digit phone number.');
                  return;
                }
                setCustomDeliveryAgents([...customDeliveryAgents, { name: newAgentName.trim(), phone: newAgentPhone.trim() }]);
                setNewAgentName('');
                setNewAgentPhone('');
                Alert.alert('Success', 'Delivery agent added successfully!');
              }}>
              <Text style={styles.primaryBtnText}>Save Agent</Text>
            </TouchableOpacity>

            <View style={{ marginTop: 10 }}>
              <Text style={{ fontWeight: 'bold', color: '#555', marginBottom: 5 }}>Current Agents & Phones:</Text>
              {customDeliveryAgents.map((ag, idx) => (
                <Text key={idx} style={{ fontSize: 13, color: '#666', paddingVertical: 2 }}>
                  • {ag.name} ({ag.phone})
                </Text>
              ))}
            </View>
          </View>

          <View style={styles.adminCard}>
            <Text style={{ fontSize: 16, fontWeight: 'bold', marginBottom: 10, color: '#333' }}>
              Manage All Product Images ({INITIAL_PRODUCTS.length} Items)
            </Text>
            <Text style={{ fontSize: 13, color: '#666', marginBottom: 10 }}>
              Paste an image URL (or Google Drive share link) below for any product to update its photo:
            </Text>
            {INITIAL_PRODUCTS.map((prod) => (
              <View key={prod.id} style={{ paddingVertical: 8, borderBottomWidth: 1, borderColor: '#eee' }}>
                <Text style={{ fontSize: 13, fontWeight: 'bold', color: '#333', marginBottom: 4 }} numberOfLines={1}>
                  {prod.id}. {prod.name}
                </Text>
                <View style={{ flexDirection: 'row', gap: 6 }}>
                  <TextInput
                    style={[styles.input, { flex: 1, marginBottom: 0, height: 36, fontSize: 12 }]}
                    placeholder="Paste Image URL..."
                    value={imageUrlInputs[prod.id] || ''}
                    onChangeText={(val) => setImageUrlInputs({ ...imageUrlInputs, [prod.id]: val })}
                  />
                  <TouchableOpacity
                    style={{ backgroundColor: '#0C447C', paddingHorizontal: 12, justifyContent: 'center', borderRadius: 4, height: 36 }}
                    onPress={() => handleUpdateProductImage(prod.id)}>
                    <Text style={{ color: '#fff', fontSize: 11, fontWeight: 'bold' }}>Save</Text>
                  </TouchableOpacity>
                </View>
              </View>
            ))}
          </View>

          <Text style={{ fontSize: 18, fontWeight: 'bold', marginVertical: 10 }}>Live Customer Orders</Text>
          {ordersLoading ? (
            <ActivityIndicator size="large" color="#0C447C" style={{ marginVertical: 20 }} />
          ) : orders.length === 0 ? (
            <Text style={{ color: '#666', textAlign: 'center', marginVertical: 20 }}>No active orders found.</Text>
          ) : (
            orders.map((ord) => (
              <View key={ord.id} style={styles.adminCard}>
                <Text style={{ fontWeight: 'bold', fontSize: 15 }}>Order ID: {ord.id.slice(-6)}</Text>
                <Text>Customer: {ord.customerName} ({ord.phone})</Text>
                <Text>Area: {ord.area} | Address: {ord.address}</Text>
                <Text>Items: {ord.items}</Text>
                <Text style={{ fontWeight: 'bold', color: '#0C447C' }}>Total: {formatRupees(Number(ord.total))}</Text>
                <Text>Status: <Text style={{ fontWeight: 'bold', color: 'green' }}>{ord.status}</Text></Text>
                {ord.deliveryBoyName ? (
                  <Text style={{ fontSize: 12, color: '#555', marginTop: 2 }}>
                    Assigned to: {ord.deliveryBoyName} ({ord.deliveryBoyPhone})
                  </Text>
                ) : null}

                <Text style={{ fontWeight: 'bold', color: '#333', marginTop: 10, marginBottom: 4 }}>Assign to Agent:</Text>
                {customDeliveryAgents.map((ag, aIdx) => (
                  <TouchableOpacity
                    key={aIdx}
                    style={[styles.primaryBtn, { backgroundColor: '#25D366', marginTop: 4 }]}
                    onPress={async () => {
                      const patchPayload = {
                        fields: {
                          deliveryBoyName: { stringValue: ag.name },
                          deliveryBoyPhone: { stringValue: ag.phone },
                          status: { stringValue: 'Out for Delivery' },
                        },
                      };
                      await fetch(FIRESTORE_BASE_URL + '/' + ord.id + '?updateMask.fieldPaths=deliveryBoyName&updateMask.fieldPaths=deliveryBoyPhone&updateMask.fieldPaths=status', {
                        method: 'PATCH',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(patchPayload),
                      });
                      loadOrders();
                      Alert.alert('Assigned', `Order assigned to ${ag.name}`);
                    }}>
                    <Text style={styles.primaryBtnText}>Assign to {ag.name}</Text>
                  </TouchableOpacity>
                ))}
              </View>
            ))
          )}
        </ScrollView>
      </SafeAreaView>
    );
  }

  if (screen === 'deliveryPortal') {
    return (
      <SafeAreaView style={styles.safeArea}>
        <View style={styles.header}>
          <Text style={styles.headerTitle}>Delivery Portal ({activeAgentPhone})</Text>
          <TouchableOpacity onPress={() => setScreen('catalog')}>
            <Ionicons name="close-circle" size={28} color="#333" />
          </TouchableOpacity>
        </View>
        <ScrollView style={{ padding: 15 }}>
          <Text style={{ fontSize: 15, fontWeight: 'bold', marginBottom: 10, color: '#333' }}>
            Assigned Orders For Your Number
          </Text>
          {ordersLoading ? (
            <ActivityIndicator size="large" color="#0C447C" style={{ marginVertical: 30 }} />
          ) : orders.filter((o) => o.deliveryBoyPhone === activeAgentPhone && o.status === 'Out for Delivery').length === 0 ? (
            <Text style={{ color: '#666', textAlign: 'center', marginVertical: 20 }}>No pending deliveries assigned to your phone number right now.</Text>
          ) : (
            orders
              .filter((o) => o.deliveryBoyPhone === activeAgentPhone && o.status === 'Out for Delivery')
              .map((ord) => (
                <View key={ord.id} style={styles.adminCard}>
                  <View style={{ flexDirection: 'row', justifyContent: 'space-between', marginBottom: 5 }}>
                    <Text style={{ fontWeight: 'bold', fontSize: 15 }}>Order #{ord.id.slice(-6)}</Text>
                    <Text style={{ fontWeight: 'bold', color: '#0C447C' }}>{ord.status}</Text>
                  </View>
                  <Text style={{ fontSize: 13, color: '#333' }}>Customer: {ord.customerName} ({ord.phone})</Text>
                  <Text style={{ fontSize: 13, color: '#333' }}>Delivery Area: {ord.area}</Text>
                  <Text style={{ fontSize: 13, color: '#555', marginBottom: 4 }}>Items: {ord.items}</Text>
                  <Text style={{ fontWeight: 'bold', color: '#0C447C', marginBottom: 8 }}>Total: {formatRupees(Number(ord.total))}</Text>
                  
                  <TouchableOpacity
                    style={[styles.primaryBtn, { backgroundColor: 'green', marginTop: 5 }]}
                    onPress={async () => {
                      const patchPayload = {
                        fields: {
                          status: { stringValue: 'Delivered' },
                        },
                      };
                      await fetch(FIRESTORE_BASE_URL + '/' + ord.id + '?updateMask.fieldPaths=status', {
                        method: 'PATCH',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(patchPayload),
                      });
                      loadOrders();
                      Alert.alert('Success', 'Order marked as Delivered!');
                    }}>
                    <Text style={styles.primaryBtnText}>Mark as Delivered</Text>
                  </TouchableOpacity>
                </View>
              ))
          )}
        </ScrollView>
      </SafeAreaView>
    );
  }

  if (screen === 'checkout') {
    return (
      <SafeAreaView style={styles.safeArea}>
        <View style={styles.header}>
          <TouchableOpacity onPress={() => setScreen('catalog')} style={{ flexDirection: 'row', alignItems: 'center', gap: 6 }}>
            <Ionicons name="arrow-back" size={22} color="#0C447C" />
            <Text style={{ fontWeight: 'bold', color: '#0C447C', fontSize: 16 }}>Back to Shop</Text>
          </TouchableOpacity>
          <Text style={styles.headerTitle}>Checkout</Text>
        </View>
        <ScrollView style={{ padding: 15 }}>
          <View style={styles.adminCard}>
            <Text style={{ fontSize: 16, fontWeight: 'bold', marginBottom: 10, color: '#333' }}>Order Summary</Text>
            {cartItemsList.map((item) => (
              <View key={item.id} style={{ flexDirection: 'row', justifyContent: 'space-between', paddingVertical: 4 }}>
                <Text style={{ fontSize: 13, color: '#444', flex: 1 }} numberOfLines={1}>
                  {item.name} x {item.qty}
                </Text>
                <Text style={{ fontSize: 13, fontWeight: 'bold', color: '#333' }}>{formatRupees(item.subtotal)}</Text>
              </View>
            ))}
            <View style={{ borderTopWidth: 1, borderColor: '#eee', marginTop: 8, paddingTop: 8 }}>
              <View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
                <Text style={{ color: '#666', fontSize: 13 }}>Delivery Fee ({checkoutArea || 'Select Area'})</Text>
                <Text style={{ fontWeight: 'bold', fontSize: 13 }}>{deliveryFee === 0 ? 'FREE' : formatRupees(deliveryFee)}</Text>
              </View>
              <View style={{ flexDirection: 'row', justifyContent: 'space-between', marginTop: 4 }}>
                <Text style={{ fontWeight: 'bold', fontSize: 15, color: '#333' }}>Grand Total</Text>
                <Text style={{ fontWeight: 'bold', fontSize: 15, color: '#0C447C' }}>{formatRupees(finalGrandTotal)}</Text>
              </View>
            </View>
          </View>

          <View style={styles.adminCard}>
            <Text style={{ fontSize: 15, fontWeight: 'bold', marginBottom: 10, color: '#333' }}>Customer Details & Address</Text>
            
            <Text style={{ fontSize: 12, color: '#666', marginBottom: 4 }}>Your Full Name</Text>
            <TextInput
              style={styles.input}
              placeholder="Type your full name..."
              value={checkoutName}
              onChangeText={setCheckoutName}
            />

            <Text style={{ fontSize: 12, color: '#666', marginBottom: 4 }}>Phone Number</Text>
            <TextInput
              style={styles.input}
              placeholder="Type your 10-digit phone number..."
              keyboardType="phone-pad"
              maxLength={10}
              value={checkoutPhone}
              onChangeText={setCheckoutPhone}
            />

            <Text style={{ fontSize: 12, color: '#666', marginBottom: 4 }}>Select Delivery Area</Text>
            <ScrollView horizontal showsHorizontalScrollIndicator={false} style={{ marginBottom: 12 }}>
              {DELIVERY_AREAS.map((area) => (
                <TouchableOpacity
                  key={area}
                  style={[styles.areaChip, checkoutArea === area && styles.areaChipActive]}
                  onPress={() => setCheckoutArea(area)}>
                  <Text style={[styles.areaChipText, checkoutArea === area && styles.areaChipTextActive]}>{area}</Text>
                </TouchableOpacity>
              ))}
            </ScrollView>

            <Text style={{ fontSize: 12, color: '#666', marginBottom: 4 }}>Detailed House/Street Address</Text>
            <TextInput
              style={styles.input}
              placeholder="Type House No, Street Name, Landmark..."
              value={userAddress}
              onChangeText={setUserAddress}
            />

            <TouchableOpacity
              style={{ alignSelf: 'flex-start', marginVertical: 6 }}
              onPress={async () => {
                if (!checkoutName.trim() || checkoutPhone.trim().length < 10 || !checkoutArea || !userAddress.trim()) {
                  Alert.alert('Incomplete', 'Please fill in all details before saving.');
                  return;
                }
                try {
                  await AsyncStorage.setItem('@dairy_user_name', checkoutName.trim());
                  await AsyncStorage.setItem('@dairy_user_phone', checkoutPhone.trim());
                  await AsyncStorage.setItem('@dairy_user_area', checkoutArea);
                  await AsyncStorage.setItem('@dairy_user_detailed_address', userAddress.trim());
                  Alert.alert('Saved', 'Your address has been saved securely on your device.');
                } catch (e) {
                  Alert.alert('Error', 'Failed to save address.');
                }
              }}>
              <Text style={{ color: '#0C447C', fontWeight: 'bold', fontSize: 13 }}>💾 Save Address for Future Orders</Text>
            </TouchableOpacity>

            <Text style={{ fontSize: 15, fontWeight: 'bold', marginVertical: 10, color: '#333' }}>Payment Method</Text>
            <View style={{ flexDirection: 'row', gap: 10, marginBottom: 15 }}>
              <TouchableOpacity
                style={[styles.areaChip, paymentMethod === 'COD' && styles.areaChipActive, { flex: 1, alignItems: 'center', paddingVertical: 10 }]}
                onPress={() => setPaymentMethod('COD')}>
                <Text style={[styles.areaChipText, paymentMethod === 'COD' && styles.areaChipTextActive]}>Cash on Delivery</Text>
              </TouchableOpacity>
              <TouchableOpacity
                style={[styles.areaChip, paymentMethod === 'UPI' && styles.areaChipActive, { flex: 1, alignItems: 'center', paddingVertical: 10 }]}
                onPress={() => setPaymentMethod('UPI')}>
                <Text style={[styles.areaChipText, paymentMethod === 'UPI' && styles.areaChipTextActive]}>UPI Payment</Text>
              </TouchableOpacity>
            </View>

            {paymentMethod === 'UPI' && (
              <View style={{ backgroundColor: '#eef2f5', padding: 12, borderRadius: 8, marginBottom: 15, alignItems: 'center' }}>
                <Text style={{ fontSize: 13, fontWeight: 'bold', color: '#0C447C', marginBottom: 4 }}>Scan or Pay via UPI</Text>
                <Text style={{ fontSize: 12, color: '#333' }}>UPI ID: {UPI_ID}</Text>
                <Text style={{ fontSize: 12, color: '#333' }}>Payee: {UPI_PAYEE_NAME}</Text>
                <Text style={{ fontSize: 12, color: 'green', fontWeight: 'bold', marginTop: 4 }}>Amount: {formatRupees(finalGrandTotal)}</Text>
              </View>
            )}

            <TouchableOpacity
              style={[styles.primaryBtn, { backgroundColor: 'green', padding: 14 }]}
              disabled={orderSubmitting}
              onPress={async () => {
                if (!checkoutName.trim() || checkoutPhone.trim().length < 10 || !checkoutArea || !userAddress.trim()) {
                  Alert.alert('Incomplete Details', 'Please type your Name, Phone Number, select an Area, and type your Detailed Address.');
                  return;
                }
                setOrderSubmitting(true);
                try {
                  const itemsSummary = cartItemsList.map((i) => `${i.name} (${i.qty})`).join(', ');
                  const payload = {
                    fields: {
                      customerName: { stringValue: checkoutName.trim() },
                      phone: { stringValue: checkoutPhone.trim() },
                      address: { stringValue: userAddress.trim() },
                      area: { stringValue: checkoutArea },
                      items: { stringValue: itemsSummary },
                      total: { integerValue: String(finalGrandTotal) },
                      status: { stringValue: 'New' },
                      createdAt: { timestampValue: new Date().toISOString() },
                    },
                  };

                  const res = await fetch(FIRESTORE_BASE_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload),
                  });

                  if (res.ok) {
                    setCart({});
                    setLookupPhoneInput(checkoutPhone.trim());
                    setSearchedPhone(checkoutPhone.trim());
                    Alert.alert('Success!', 'Your order has been placed successfully!', [
                      {
                        text: 'View My Orders',
                        onPress: () => {
                          loadOrders();
                          setScreen('myOrders');
                        },
                      },
                    ]);
                  } else {
                    throw new Error('Failed to save order');
                  }
                } catch (e) {
                  Alert.alert('Error', 'Failed to place order. Please try again.');
                } finally {
                  setOrderSubmitting(false);
                }
              }}>
              {orderSubmitting ? (
                <ActivityIndicator color="#fff" />
              ) : (
                <Text style={[styles.primaryBtnText, { fontSize: 16 }]}>Confirm & Place Order</Text>
              )}
            </TouchableOpacity>
          </View>
        </ScrollView>
      </SafeAreaView>
    );
  }

  if (screen === 'myOrders') {
    const myFilteredOrders = searchedPhone
      ? orders.filter((o) => o.phone === searchedPhone)
      : [];

    return (
      <SafeAreaView style={styles.safeArea}>
        <View style={styles.header}>
          <Text style={styles.headerTitle}>My Orders & Tracking</Text>
          <TouchableOpacity onPress={() => setScreen('catalog')}>
            <Ionicons name="close-circle" size={28} color="#333" />
          </TouchableOpacity>
        </View>
        <ScrollView style={{ padding: 15 }}>
          <View style={styles.adminCard}>
            <Text style={{ fontSize: 14, fontWeight: 'bold', color: '#333', marginBottom: 6 }}>
              Enter Your Phone Number to Track Orders
            </Text>
            <View style={{ flexDirection: 'row', gap: 8 }}>
              <TextInput
                style={[styles.input, { flex: 1, marginBottom: 0 }]}
                placeholder="Type 10-digit phone number..."
                keyboardType="phone-pad"
                maxLength={10}
                value={lookupPhoneInput}
                onChangeText={setLookupPhoneInput}
              />
              <TouchableOpacity
                style={[styles.primaryBtn, { marginTop: 0, paddingHorizontal: 16, justifyContent: 'center' }]}
                onPress={() => {
                  if (!lookupPhoneInput.trim() || lookupPhoneInput.trim().length < 10) {
                    Alert.alert('Invalid', 'Please enter a valid 10-digit phone number.');
                    return;
                  }
                  setSearchedPhone(lookupPhoneInput.trim());
                  loadOrders();
                }}>
                <Text style={styles.primaryBtnText}>View Orders</Text>
              </TouchableOpacity>
            </View>
          </View>

          {ordersLoading ? (
            <ActivityIndicator size="large" color="#0C447C" style={{ marginVertical: 30 }} />
          ) : !searchedPhone ? (
            <View style={{ alignItems: 'center', marginTop: 30 }}>
              <Ionicons name="search-outline" size={50} color="#ccc" />
              <Text style={{ color: '#666', marginTop: 10, fontSize: 14, textAlign: 'center' }}>
                Please enter your phone number above and tap "View Orders" to see your tracking details.
              </Text>
            </View>
          ) : myFilteredOrders.length === 0 ? (
            <View style={{ alignItems: 'center', marginTop: 30 }}>
              <Ionicons name="receipt-outline" size={60} color="#ccc" />
              <Text style={{ color: '#666', marginTop: 10, fontSize: 16 }}>No orders found for {searchedPhone}.</Text>
              <TouchableOpacity
                style={[styles.primaryBtn, { marginTop: 20, paddingHorizontal: 20 }]}
                onPress={() => setScreen('catalog')}>
                <Text style={styles.primaryBtnText}>Start Shopping</Text>
              </TouchableOpacity>
            </View>
          ) : (
            myFilteredOrders.map((ord) => (
              <View key={ord.id} style={styles.adminCard}>
                <View style={{ flexDirection: 'row', justifyContent: 'space-between', marginBottom: 5 }}>
                  <Text style={{ fontWeight: 'bold', fontSize: 15 }}>Order #{ord.id.slice(-6)}</Text>
                  <Text style={{ fontWeight: 'bold', color: ord.status === 'Delivered' ? 'green' : '#0C447C' }}>
                    {ord.status}
                  </Text>
                </View>
                <Text style={{ fontSize: 13, color: '#555', marginBottom: 4 }}>Items: {ord.items}</Text>
                <Text style={{ fontSize: 13, color: '#555', marginBottom: 4 }}>Delivery Area: {ord.area}</Text>
                <Text style={{ fontWeight: 'bold', color: '#0C447C', marginBottom: 8 }}>Total: {formatRupees(Number(ord.total))}</Text>
                
                {ord.deliveryBoyName ? (
                  <View style={{ backgroundColor: '#eef2f5', padding: 8, borderRadius: 6, marginTop: 4 }}>
                    <Text style={{ fontSize: 12, fontWeight: 'bold', color: '#333' }}>
                      Delivery Agent: {ord.deliveryBoyName} ({ord.deliveryBoyPhone})
                    </Text>
                  </View>
                ) : (
                  <Text style={{ fontSize: 12, color: '#888', fontStyle: 'italic' }}>
                    Waiting for admin to assign a delivery agent...
                  </Text>
                )}
              </View>
            ))
          )}
        </ScrollView>
      </SafeAreaView>
    );
  }

  return (
    <SafeAreaView style={styles.safeArea}>
      <StatusBar barStyle="dark-content" backgroundColor="#fff" />
      <View style={styles.header}>
        <View>
          <Text style={styles.headerTitle}>Dairy Nest</Text>
          <Text style={styles.headerSub}>Chhapra</Text>
        </View>
        <View style={{ flexDirection: 'row', gap: 6 }}>
          <TouchableOpacity
            onPress={() => {
              setLookupPhoneInput(checkoutPhone || userPhone || '');
              setSearchedPhone(checkoutPhone || userPhone || '');
              loadOrders();
              setScreen('myOrders');
            }}
            style={styles.iconBtn}>
            <Ionicons name="receipt-outline" size={22} color="#0C447C" />
          </TouchableOpacity>
          <TouchableOpacity onPress={() => setScreen('login')} style={styles.iconBtn}>
            <Ionicons name="settings-outline" size={22} color="#0C447C" />
          </TouchableOpacity>
        </View>
      </View>

      <View style={{ paddingHorizontal: 15, paddingVertical: 8 }}>
        <TouchableOpacity style={styles.whatsappBanner} onPress={handleWhatsAppOrder}>
          <Ionicons name="logo-whatsapp" size={20} color="#fff" style={{ marginRight: 8 }} />
          <Text style={{ color: '#fff', fontWeight: 'bold', fontSize: 14 }}>
            Quick Order via WhatsApp / Voice Note
          </Text>
        </TouchableOpacity>
      </View>

      <View style={{ paddingHorizontal: 15, marginBottom: 8 }}>
        <View style={styles.searchBox}>
          <Ionicons name="search" size={18} color="#666" style={{ marginRight: 8 }} />
          <TextInput
            style={{ flex: 1, height: 38, fontSize: 14 }}
            placeholder="Search Amul milk, paneer, butter..."
            value={searchQuery}
            onChangeText={setSearchQuery}
          />
          {searchQuery ? (
            <TouchableOpacity onPress={() => setSearchQuery('')}>
              <Ionicons name="close-circle" size={18} color="#666" />
            </TouchableOpacity>
          ) : null}
        </View>
      </View>

      <View style={{ height: 44 }}>
        <ScrollView horizontal showsHorizontalScrollIndicator={false} contentContainerStyle={{ paddingHorizontal: 15, alignItems: 'center' }}>
          {CATEGORIES.map((cat) => (
            <TouchableOpacity
              key={cat}
              style={[styles.catChip, activeCat === cat && styles.catChipActive]}
              onPress={() => {
                setActiveCat(cat);
                setSearchQuery('');
              }}>
              <Text style={[styles.catText, activeCat === cat && styles.catTextActive]}>{cat}</Text>
            </TouchableOpacity>
          ))}
        </ScrollView>
      </View>

      <FlatList
        data={INITIAL_PRODUCTS.filter((p) => {
          const matchesCat = activeCat === 'All' || p.cat === activeCat;
          const matchesSearch = p.name.toLowerCase().includes(searchQuery.toLowerCase());
          return searchQuery ? matchesSearch : matchesCat;
        })}
        keyExtractor={(item) => item.id}
        contentContainerStyle={{ padding: 15, paddingBottom: 80 }}
        renderItem={({ item }) => {
          const isOut = stockMap[item.id];
          const customImg = imageMap[item.id];
          const qty = cart[item.id] || 0;
          return (
            <View style={styles.productCard}>
              <View style={styles.productIconBox}>
                {customImg ? (
                  <Image source={{ uri: customImg }} style={{ width: 44, height: 44, borderRadius: 8 }} />
                ) : (
                  <Ionicons name={item.icon || 'cube'} size={24} color="#0C447C" />
                )}
              </View>
              <View style={{ flex: 1, marginLeft: 12 }}>
                <Text style={styles.productName}>{item.id}. {item.name}</Text>
                <Text style={styles.productPrice}>{formatRupees(item.price)}</Text>
                {isOut ? <Text style={styles.outOfStock}>Out of Stock</Text> : null}
              </View>
              {!isOut && (
                <View style={{ flexDirection: 'row', alignItems: 'center', gap: 8 }}>
                  {qty > 0 ? (
                    <>
                      <TouchableOpacity
                        style={styles.qtyBtn}
                        onPress={() => setCart({ ...cart, [item.id]: qty - 1 })}>
                        <Text style={styles.qtyBtnText}>-</Text>
                      </TouchableOpacity>
                      <Text style={{ fontWeight: 'bold' }}>{qty}</Text>
                      <TouchableOpacity
                        style={styles.qtyBtn}
                        onPress={() => setCart({ ...cart, [item.id]: qty + 1 })}>
                        <Text style={styles.qtyBtnText}>+</Text>
                      </TouchableOpacity>
                    </>
                  ) : (
                    <TouchableOpacity
                      style={styles.addBtn}
                      onPress={() => setCart({ ...cart, [item.id]: 1 })}>
                      <Text style={styles.addBtnText}>ADD</Text>
                    </TouchableOpacity>
                  )}
                </View>
              )}
            </View>
          );
        }}
      />

      {cartItemsList.length > 0 && (
        <View style={styles.cartBar}>
          <View>
            <Text style={{ color: '#fff', fontSize: 12 }}>{cartItemsList.length} items added</Text>
            <Text style={{ color: '#fff', fontWeight: 'bold', fontSize: 16 }}>{formatRupees(cartTotalAmount)}</Text>
          </View>
          <TouchableOpacity
            style={styles.proceedBtn}
            onPress={() => {
              setCheckoutName('');
              setCheckoutPhone('');
              setCheckoutArea('');
              setUserAddress('');
              setScreen('checkout');
            }}>
            <Text style={{ color: '#0C447C', fontWeight: 'bold', fontSize: 15 }}>Proceed to Checkout</Text>
          </TouchableOpacity>
        </View>
      )}
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  safeArea: { flex: 1, backgroundColor: '#f8f9fa' },
  header: { flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center', padding: 15, backgroundColor: '#fff', borderBottomWidth: 1, borderBottomColor: '#eee' },
  headerTitle: { fontSize: 18, fontWeight: 'bold', color: '#0C447C' },
  headerSub: { fontSize: 12, color: '#666' },
  iconBtn: { padding: 8, position: 'relative' },
  whatsappBanner: { flexDirection: 'row', backgroundColor: '#25D366', padding: 10, borderRadius: 8, alignItems: 'center', justifyContent: 'center' },
  searchBox: { flexDirection: 'row', alignItems: 'center', backgroundColor: '#fff', borderWidth: 1, borderColor: '#ddd', borderRadius: 8, paddingHorizontal: 10, height: 40 },
  catChip: { paddingHorizontal: 14, paddingVertical: 6, backgroundColor: '#eef2f5', borderRadius: 20, marginRight: 8, height: 32 },
  catChipActive: { backgroundColor: '#0C447C' },
  catText: { fontSize: 13, color: '#444' },
  catTextActive: { color: '#fff', fontWeight: 'bold' },
  productCard: { flexDirection: 'row', alignItems: 'center', backgroundColor: '#fff', padding: 12, borderRadius: 10, marginBottom: 10, borderWidth: 1, borderColor: '#eee' },
  productIconBox: { width: 44, height: 44, backgroundColor: '#eef2f5', borderRadius: 8, justifyContent: 'center', alignItems: 'center' },
  productName: { fontSize: 14, fontWeight: 'bold', color: '#333' },
  productPrice: { fontSize: 13, color: '#0C447C', marginTop: 2, fontWeight: '600' },
  outOfStock: { color: 'red', fontSize: 11, marginTop: 2 },
  addBtn: { backgroundColor: '#0C447C', paddingHorizontal: 16, paddingVertical: 6, borderRadius: 4 },
  addBtnText: { color: '#fff', fontWeight: 'bold', fontSize: 12 },
  qtyBtn: { backgroundColor: '#ddd', paddingHorizontal: 10, paddingVertical: 4, borderRadius: 4 },
  qtyBtnText: { fontWeight: 'bold' },
  regContainer: { flex: 1, justifyContent: 'center', padding: 20, backgroundColor: '#f5f5f5' },
  regCard: { backgroundColor: '#fff', padding: 20, borderRadius: 12, borderWidth: 1, borderColor: '#eee' },
  logoBadge: { width: 60, height: 60, backgroundColor: '#eef2f5', borderRadius: 30, justifyContent: 'center', alignItems: 'center', alignSelf: 'center', marginBottom: 10 },
  regTitle: { fontSize: 20, fontWeight: 'bold', textAlign: 'center', color: '#333' },
  regSubtitle: { fontSize: 13, textAlign: 'center', color: '#666', marginBottom: 20 },
  input: { borderWidth: 1, borderColor: '#ccc', borderRadius: 6, padding: 10, marginBottom: 10, backgroundColor: '#fafafa' },
  label: { fontSize: 13, fontWeight: 'bold', color: '#444', marginBottom: 6 },
  areaChip: { paddingHorizontal: 12, paddingVertical: 8, backgroundColor: '#eee', borderRadius: 6, marginRight: 8 },
  areaChipActive: { backgroundColor: '#0C447C' },
  areaChipText: { fontSize: 12, color: '#333' },
  areaChipTextActive: { color: '#fff', fontWeight: 'bold' },
  primaryBtn: { backgroundColor: '#0C447C', padding: 12, borderRadius: 6, alignItems: 'center', marginTop: 5 },
  primaryBtnText: { color: '#fff', fontWeight: 'bold', fontSize: 14 },
  errorText: { color: 'red', fontSize: 12, marginBottom: 10 },
  adminCard: { backgroundColor: '#fff', padding: 15, borderRadius: 8, marginBottom: 12, borderWidth: 1, borderColor: '#ddd' },
  cartBar: { position: 'absolute', bottom: 0, left: 0, right: 0, backgroundColor: '#0C447C', padding: 15, flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center', elevation: 10 },
  proceedBtn: { backgroundColor: '#fff', paddingHorizontal: 16, paddingVertical: 10, borderRadius: 6 }


