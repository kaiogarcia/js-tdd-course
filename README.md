import React from 'react';
import { View, Image, TouchableOpacity, TextInput, StyleSheet } from 'react-native';
import { FontAwesome } from '@expo/vector-icons';

const Header = ({ onSearch }) => {
  return (
    <View style={styles.container}>
      <Image source={require('../assets/github_logo.png')} style={styles.logo} />
      <TouchableOpacity style={styles.searchButton} onPress={onSearch}>
        <FontAwesome name="search" size={24} color="#040404" />
      </TouchableOpacity>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flexDirection: 'row',
    alignItems: 'center',
    backgroundColor: '#040404',
    padding: 10,
  },
  logo: {
    width: 120,
    height: 40,
    resizeMode: 'contain',
  },
  searchButton: {
    marginLeft: 'auto',
  },
});

export default Header;
